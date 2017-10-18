---
title: Java List
date: 2017-10-17 20:01:35
tags:
- Java
- List

---




## Introduction

List is an interface in Java. It defines all the common behaviors that a list data structure should have. We know it very well. We use it every day. However, do we understand all of its implmentations? Have we discovered all its functionalities which we don't know before? Can we create our own List implementation than JDK? Seatbelt on, old driver's car is starting the engine.

Let's start with its documentation.

> An <u>ordered collection</u> (also known as a *sequence*). The user of this interface has precise control over where in the list each element is inserted. The user can access elements by their <u>integer index</u> (position in the list), and search for elements in the list.

Straightforward, isn't it? Basically, it's an orderred array which is accessed by call function with index. That's how we use it on its concrete class: **ArrayList**. Ok, done with all the bullshit. Let's start looking at its some interesting functions with their implementations.

## ArrayList

```java
public boolean add(E e) {
  ensureCapacityInternal(size + 1);  // Increments modCount!!
  elementData[size++] = e;
  return true;
}
```

If you dig into `add` function, you will see, basically, everything is stored in an array. However, the difference lies in this function `grow`.

```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

It means, when the size of the list reaches the current length of the array, it will expand its size by 50% `oldCapacity + (oldCapacity >> 1)`. Then it will copy all the data. Here is where our algorithm class amotised analysis pays off.

Same idea will apply to add with index, add all.

------

```java
public boolean contains(Object o) {
  return indexOf(o) >= 0;
}
public int indexOf(Object o) {
  if (o == null) {
    for (int i = 0; i < size; i++)
      if (elementData[i]==null)
        return i;
  } else {
    for (int i = 0; i < size; i++)
      if (o.equals(elementData[i]))
        return i;
  }
  return -1;
}
```

The `contains` function is straightforward. Linear search for the particular element. We should notice it uses `equals` function rather than `==` when the element is not null, why is that? If we use `==`, what will happen?

------

There are 2 functions that I want to mention which are useful but somehow hard to understand: `iterator` and `listIterator`.

You can think these 2 guys as cursors. But `iterator` can only go one direction and never come back while `listIterator` can go both directions.

------

```java
private class Itr implements Iterator<E> {
    int cursor;       // index of next element to return
    int lastRet = -1; // index of last element returned; -1 if no such
    int expectedModCount = modCount;

    public boolean hasNext() {
        return cursor != size;
    }

    @SuppressWarnings("unchecked")
    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        return (E) elementData[lastRet = i];
    }

    public void remove() {
        if (lastRet < 0)
            throw new IllegalStateException();
        checkForComodification();

        try {
            ArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;
            expectedModCount = modCount;
        } catch (IndexOutOfBoundsException ex) {
            throw new ConcurrentModificationException();
        }
    }

    @Override
    @SuppressWarnings("unchecked")
    public void forEachRemaining(Consumer<? super E> consumer) {
        Objects.requireNonNull(consumer);
        final int size = ArrayList.this.size;
        int i = cursor;
        if (i >= size) {
            return;
        }
        final Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length) {
            throw new ConcurrentModificationException();
        }
        while (i != size && modCount == expectedModCount) {
            consumer.accept((E) elementData[i++]);
        }
        // update once at end of iteration to reduce heap write traffic
        cursor = i;
        lastRet = i - 1;
        checkForComodification();
    }

    final void checkForComodification() {
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
}
```

It turns out in ArrayList, iterator is a subclass which implements the `Iterator` interface. Reading code could be overwhelming so let's just scan over it and see what we can learn.

As we said, an iterator is just an cursor and it's defined on the top. So, it's just like an for-loop in an iteration way. One thing to point out is the exception: `ConcurrentModificationException`. This exception is mostly seen among newbies in Java. Let's take a look at its definition:

> This exception may be thrown by methods that have detected concurrent modification of an object when such modification is not permissible.

What does it mean? It can be simply interpreted as no support for concurrent opertaions. You can't share the iterator across threads.

> The iterators returned by this class's iterator and listIterator methods are fail-fast: if the vector is structurally modified at any time after the iterator is created, in any way except through the iterator's own remove or add methods, the iterator will throw a ConcurrentModificationException.

Moreover, you probably saw this exception being thrown even in the same thread. When is that? When you iterate over a list and you call the `remove` function, you will see it. That means an iterator can only do one thing at a time. Once the current element is removed by one operation from the iterator, the iterator will move to the next one, but the size of the list may not get updated in time and that's when you will see the exception. Like this:

```java
// will throw ConcurrentModificationException
for (int i : arraysOfInteger) {
    arraysOfInteger.remove(i);
}
```



## LinkedList

Now let's take a look at `LinkedList`. What is so special for it from `ArrayList`? See the code snippet below:

```java
private static class Node<E> {
  E item;
  Node<E> next;
  Node<E> prev;

  Node(Node<E> prev, E element, Node<E> next) {
    this.item = element;
    this.next = next;
    this.prev = prev;
  }
}
```

This is an inner class in `LinkedList`, which gives the idea that it's a list linked by `next` and `prev` pointer rather than Array. It makes it possible to add or remove an item to the end and at the head. There is no need to expand or shrink.

## Vector

This class is an old class, which is even older than `ArrayList`. Overall, it is as same as `ArrayList` with the only difference — thread safe, which means `Vector` can be accessed by multiple threads while `ArrayList` it non-thread safe.

## Summary

These 3 classes under `List` interface are mostly used for daily job. There are of course, more classes that implement `List` interface, but they won't be covered here. Recommendations are to explore them by yourself.

Happy Coding.
