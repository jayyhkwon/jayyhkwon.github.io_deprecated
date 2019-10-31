---
layout: post
title:  " ArrayList 시간복잡도 "
date:   2019-10-31 22:30 +0530
categories: DataStructure JAVA
---

ArrayList 시간복잡도

ArrayList의 메서드 get(), add(), remove()의 시간복잡도를 알아보자.

## 목차
1. get(int index)
2. add()
    - add(E element)
    - add(int index, E element)
3. remove(int index)
##


### 1\. get(int index)

```java

public E get(int index) {
    rangeCheck(index);

    return elementData(index);
}

E elementData(int index) {
    return (E) elementData[index];
}

```

ArrayList는 Obect[]로 구현되어 있으므로 랜덤엑세스가 가능하다.
따라서 시간복잡도 : O(1)

### 2\. add()

add()의 경우 배열의 마지막에 element를 추가하는 add(E element) 메서드와 <br/>
특정 인덱스에 element를 추가하는 add(int index, E element) 메서드로 나뉜다.<br/>

#### 2-1\. add(int index)
```java

public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}


```

메서드는 크게 두 부분으로 나뉜다.
    - 배열의 크기를 확인하는 부분 -> "ensureCapacityInternal(size + 1)"
    - 배열의 lastIndex에 element를 추가하는 부분 -> "elementData[size++] = e;"

배열의 크기를 확인하는 부분을 까보면 <br/>
size >= length일 경우에 grow() 메서드가 실행됨을 알 수 있다.

```java

private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}   

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    // size >= length이므로 grow() 메서드 호출
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

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

grow() 메서드는 배열을 복사하므로 이 작업의 시간복잡도는 배열의 원소의 갯수 n에 비례한다.
따라서 <b>배열의 크기를 확인하는 부분의 시간복잡도는 O(n)이다.</b>

<b>배열에 lastIndex에 element를 추가하는 부분의 시간복잡도는 get()과 마찬가지로 O(1)이다.</b>

즉 add(E element)는 시간복잡도 O(n)과 O(1)이 공존하는데
이를 계산하기 위해 분할상환분석을 사용한다.

<a href="https://ko.wikipedia.org/wiki/%EB%B6%84%ED%95%A0%EC%83%81%ED%99%98%EB%B6%84%EC%84%9D">분할상관분석</a>에 의해
<b>배열의 크기를 증가하는 작업(시간복잡도가 O(n))보다는 배열의 lastIndex에 element를 추가하는 작업이 훨씬 많으므로 이를 평균내면 O(1)의 시간복잡도를 가진다.</b>


#### 2-2\. add(int index, E element)

```java

public void add(int index, E element) {
    rangeCheckForAdd(index);

    ensureCapacityInternal(size + 1);  // Increments modCount!!
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;
    size++;
}

```

해당 index의 오른쪽에 존재하는 모든 element들의 index를 옮겨야 하므로
시간복잡도는 배열의 원소의 갯수 n에 비례한다.
따라서 시간복잡도는 O(n)이다.

### 3\. remove(int index)

```java

public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}

```

해당 index의 오른쪽에 존재하는 모든 element들의 index를 옮겨야 하므로
시간복잡도는 배열의 원소의 갯수 n에 비례한다.
따라서 시간복잡도는 O(n)이다.



ref. <a>https://stackoverflow.com/questions/45220908/why-arraylist-add-and-addint-index-e-complexity-is-amortized-constant-time</a>

ref. <a>https://ko.wikipedia.org/wiki/%EB%B6%84%ED%95%A0%EC%83%81%ED%99%98%EB%B6%84%EC%84%9D</a>


