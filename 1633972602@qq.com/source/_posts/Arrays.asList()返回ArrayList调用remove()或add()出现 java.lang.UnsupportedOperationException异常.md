---
title: Arrays.asList()返回ArrayList调用remove()或add()出现 java.lang.UnsupportedOperationException异常
categories: Java
tags: java
date: 2017-04-24 11:44:32
---

* Iterator和ListIterator可以对集合遍历同时可以做remove操作, 但在调用Arrays.asList()转化为ArrayList的过程中并不能调用remove()

<!-- more -->

```
public class Test {

    public static void main(String[] args) {
        List<String> list = test();
        Iterator<String> it = list.iterator();
        while (it.hasNext()) {
            String str = it.next();
            System.out.println(str);
            if (str.equals("a")) {
                it.remove();     // TODO: 在这出错了!!! java.lang.UnsupportedOperationException
            }
        }
    }

    private static List<String> test() {
        // TODO: 返回的是java.util.Arrays$ArrayList
        List<String> list = Arrays.asList("a", "b", "c");  
        list.sort( (e1, e2 ) -> e1.compareTo(e2) );
        return list;
    }

}

```
异常:
```
Exception in thread "main" java.lang.UnsupportedOperationException
	at java.util.AbstractList.remove(AbstractList.java:161)
	at java.util.AbstractList$Itr.remove(AbstractList.java:374)
	at lambda.main(lambda.java:18)
```

解决方式:

```
  private static List<String> test() {
        // TODO: 转化成java.util.ArrayList
	List<String> list = new ArrayList(Arrays.asList("a", "b", "c"));
        list.sort( (e1, e2 ) -> e1.compareTo(e2) );
        return list;
    }
```




什么原因呢? Arrays.asList()返回的是ArrayList没错, 但是返回的ArrayList是Array的一个内部类（java.util.Arrays$ArrayList）, 源码贴到下面了， 观察一下, 根本没有java.util.ArrayList类中的add()、remove()方法, 调用remove()方法当然出错啦！

```[java.util.Arrays$ArrayList 源码]
...
    public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
    }

    private static class ArrayList<E> extends AbstractList<E>
        implements RandomAccess, java.io.Serializable
    {
        private static final long serialVersionUID = -2764017481108945198L;
        private final E[] a;

        ArrayList(E[] array) {
            a = Objects.requireNonNull(array);
        }

        @Override
        public int size() {
            return a.length;
        }

        @Override
        public Object[] toArray() {
            return a.clone();
        }

        @Override
        @SuppressWarnings("unchecked")
        public <T> T[] toArray(T[] a) {
            int size = size();
            if (a.length < size)
                return Arrays.copyOf(this.a, size,
                                     (Class<? extends T[]>) a.getClass());
            System.arraycopy(this.a, 0, a, 0, size);
            if (a.length > size)
                a[size] = null;
            return a;
        }

        @Override
        public E get(int index) {
            return a[index];
        }

        @Override
        public E set(int index, E element) {
            E oldValue = a[index];
            a[index] = element;
            return oldValue;
        }

        @Override
        public int indexOf(Object o) {
            E[] a = this.a;
            if (o == null) {
                for (int i = 0; i < a.length; i++)
                    if (a[i] == null)
                        return i;
            } else {
                for (int i = 0; i < a.length; i++)
                    if (o.equals(a[i]))
                        return i;
            }
            return -1;
        }

        @Override
        public boolean contains(Object o) {
            return indexOf(o) != -1;
        }

        @Override
        public Spliterator<E> spliterator() {
            return Spliterators.spliterator(a, Spliterator.ORDERED);
        }

        @Override
        public void forEach(Consumer<? super E> action) {
            Objects.requireNonNull(action);
            for (E e : a) {
                action.accept(e);
            }
        }

        @Override
        public void replaceAll(UnaryOperator<E> operator) {
            Objects.requireNonNull(operator);
            E[] a = this.a;
            for (int i = 0; i < a.length; i++) {
                a[i] = operator.apply(a[i]);
            }
        }

        @Override
        public void sort(Comparator<? super E> c) {
            Arrays.sort(a, c);
        }
    }
    ...
```
