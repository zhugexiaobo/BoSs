---
title: NSMutableString as retain/copy
layout: post
tags: iOS 
---

> 来自 [SOF](http://stackoverflow.com/a/5004019/1395469) 的一个回答。可变数据类型不应该直接用 `@Property` 创建，而是自定义一个存取的类。

-----------
(note: the NSMutableString specific response is a ways down)

> I have aa number of NSMutableString's in my app (almost 10-11); all defined as ivar/property
>
> `@property (nonatomic, retain) NSMutableString *str1;`
> I read somewhere that it is better to use "copy" for strings. Is that true?

yes, copying NSString (not talking about NSMutableString quite yet) should be the default. the reason (as stated by other posters) is that the implementation of an immutable instance can simply `retain` itself in its implementation of `copyWithZone:`. also, `copy` gives you the expected behavior. in other words, there is not a use for retaining a string if it is in fact immutable. in order to guarantee that you're dealing with an immutable string, you simply copy at creation and set.

> If yes, can I just replace retain to copy in my app and remove the release in dealloc?

`copy`, like `retain` returns an object which you must explicitly release (e.g., in dealloc) in non-garbage collected environments. immutable strings are still reference-counted. there are exceptions to this, notably:

- CF/NS-String literals exist for the program's duration

- you can use CF-APIs to create strings which are never released, or are managed beyond the scope of traditional retain/release mechanisms

- NS-Type subclasses could implement another scheme (although usual considerations of this are flawed)

> Do I need to consider some other things as well?

now we get to the NSMutable-type details. the implementation of property syntax invokes `copy`. as we know, `-[NSMutableString copy]` returns an immutable NSString. this is an issue waiting to happen for a naive implementation, since your NSMutableString ivar is now an NSString, using default-synthesized setter implementation.

```
@property (nonatomic, retain) NSMutableString *str1;
// or
@property (nonatomic, copy) NSMutableString *str1;
```

when declaring:

```
@property (nonatomic, retain) NSMutableString *str1;
```
you may use the default synthesized-implementation.

but there's a twist when declaring:

```
@property (nonatomic, copy) NSMutableString *str1;
```
you should *not* use the default synthesized-implementation. instead, you should **write your own**:

```
- (void)setStr1:(NSMutableString *)arg
{
	/* locking/observing/undo/etc omitted */
	NSMutableString * copy = [arg mutableCopy];
	NSMutableString * prev = str1;
	str1 = copy;
	[prev release];
}

- (NSMutableString *)str1
{
    /* locking/observing/etc omitted */
    /* don't return something the clients thinks they may
        possibly modify, and don't return something you may
        modify behind their back
    */
	return [[str1 mutableCopy] autorelease];
    // -- or???
	return [[str1 retain] autorelease];
}
```
hmm... that's not very clear, and not very considerate for clients. it also implies a lot of unnecessary things, and introduces additional copy-overhead. let's re-evaluate this.


additional considerations/problems at this point:

- the default-setter requires that the client makes a mutableCopy of the string, if they only hold an immutable string. that is immediately turned into a unique copy by you, in the setter, since you can't expect the client to create a unique mutableCopy just for you -- besides, that's the duty of class's implementation. so NSMutableString is a bad argument for the setter. NSString is the appropriate setter, unless you're retaining the string.

- NSMutableString is also an ambiguous getter -- see the implementation. does it copy+autorelease or retain+autorelease? there isn't a standard expectation for the general form. also, clients may depend on one specific behavior. this really is an implementation detail of the class, unless you're dealing with tightly coupled objects. tightly coupled objects are those which are basically co-dependent, and not generally reusable in other contexts. that concept's fine, you may just be using a private class for several reasons. in either case, copy/write semantics to external clients and subclasses is not (implicitly) clear, and the implementation is dangerous.

- sharing an NSMutableString is generally a bad design. NSMutableString is not thread-safe. it makes no sense for clients to access and mutate the string - it's dangerous. any design which shared objects which are not thread safe should be considered with care. sharing in this case also extends to subclass usage (so make it `@private`, wherever possible)

- retain is also generally a bad design -- the clients are not going to know when the string's been (or is being) modified externally, unless you add equivalent external code to support that.

- depending on how the interface is used, this can also introduce a lot of unnecessary copying. boo.

ok - now we're pretty convinced our 'improvement' is still a bad idea in most cases =) how can we improve the design?


you should, apart from exceptional cases of tightly coupled objects, declare/implement your property as follows:

```
@interface MONThing : NSObject
{
@private
	NSMutableString * str1;
}

/* note: passes/returns NSString */
@property (nonatomic, copy) NSString * str1;

@end

@implementation MONThing

// no need for clients to create a mutableCopy
- (void)setStr1:(NSString *)arg
{
    /* locking/observing/undo/etc omitted */
	NSMutableString * copy = [arg mutableCopy];
	NSMutableString * prev = str1;
	str1 = copy;
	[prev release];
}

// the result is clearly defined. return a copy for
// thread-safety, expected behavior, and to minimize
// further copies when handling the result.
- (NSString *)str1
{
    /* locking/observing/etc omitted */
    /* don't return something the clients thinks they
        may possibly modify, and don't return something
        you may modify behind their back
    */
	return [[str1 copy] autorelease];
}

@end
```

your interface is now improved, more correct, requires less documentation and performs better.

you can also remove publicly visible accessors if you don't need them.

we can live with that interface, where it is required.

but there's more! as it turns out, the interface is necessary less often than many people think. we can avoid many of the issues altogether in most cases by simply avoiding use of NSMutableString as an ivar where possible.

as mentioned earlier, NSMutableString is not thread-safe, it's easier and more efficient in many cases to use a (copied) NSString ivar when your string is small, or does not change frequently. by adding the mutable string to the interface (as above), you'll have to guarantee thread-safety manually. in many cases, it is optimal to do something along these lines:

```
@interface MONThing : NSObject
{
	NSString * str1;
}

@property (nonatomic, copy) NSString * str1;

@end

@implementation MONThing

@synthesize str1;

- (void)updateTimeElapsed:(NSTimeInterval)seconds
{
	NSMutableString * s = [NSMutableString stringWithFormat:@"%f seconds", seconds];
	/* ...some mutations... */
	self.str1 = s;
}

@end
```  
of course, there will be a few exceptions to this -- where you will *need* a mutable ivar, but it's best to use immutable ivars where possible, and where in doubt, rather than introducing a ton of threading complexities. in most cases, you can remove the use of mutable strings from interfaces, and build the strings when necessary (as demonstrated above).

collections (NSMutableArray, NSMutableDictionary, NSMutableSet, etc), on the other hand, are required more often in implementations, and are more complex in general.


good luck!

