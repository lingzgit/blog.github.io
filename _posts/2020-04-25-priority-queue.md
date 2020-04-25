优先级队列PriorityQueue

首先来看看源码中PriorityQueue的注释：

> ```java
> /**
>  * An unbounded priority {@linkplain Queue queue} based on a priority heap.
>  * The elements of the priority queue are ordered according to their
>  * {@linkplain Comparable natural ordering}, or by a {@link Comparator}
>  * provided at queue construction time, depending on which constructor is
>  * used.  A priority queue does not permit {@code null} elements.
>  * A priority queue relying on natural ordering also does not permit
>  * insertion of non-comparable objects (doing so may result in
>  * {@code ClassCastException}).
>  *
>  * <p>The <em>head</em> of this queue is the <em>least</em> element
>  * with respect to the specified ordering.  If multiple elements are
>  * tied for least value, the head is one of those elements -- ties are
>  * broken arbitrarily.  The queue retrieval operations {@code poll},
>  * {@code remove}, {@code peek}, and {@code element} access the
>  * element at the head of the queue.
>  *
>  * <p>A priority queue is unbounded, but has an internal
>  * <i>capacity</i> governing the size of an array used to store the
>  * elements on the queue.  It is always at least as large as the queue
>  * size.  As elements are added to a priority queue, its capacity
>  * grows automatically.  The details of the growth policy are not
>  * specified.
>  *
>  * <p>This class and its iterator implement all of the
>  * <em>optional</em> methods of the {@link Collection} and {@link
>  * Iterator} interfaces.  The Iterator provided in method {@link
>  * #iterator()} is <em>not</em> guaranteed to traverse the elements of
>  * the priority queue in any particular order. If you need ordered
>  * traversal, consider using {@code Arrays.sort(pq.toArray())}.
>  *
>  * <p><strong>Note that this implementation is not synchronized.</strong>
>  * Multiple threads should not access a {@code PriorityQueue}
>  * instance concurrently if any of the threads modifies the queue.
>  * Instead, use the thread-safe {@link
>  * java.util.concurrent.PriorityBlockingQueue} class.
>  *
>  * <p>Implementation note: this implementation provides
>  * O(log(n)) time for the enqueuing and dequeuing methods
>  * ({@code offer}, {@code poll}, {@code remove()} and {@code add});
>  * linear time for the {@code remove(Object)} and {@code contains(Object)}
>  * methods; and constant time for the retrieval methods
>  * ({@code peek}, {@code element}, and {@code size}).
>  *
>  * <p>This class is a member of the
>  * <a href="{@docRoot}/../technotes/guides/collections/index.html">
>  * Java Collections Framework</a>.
>  *
>  * @since 1.5
>  * @author Josh Bloch, Doug Lea
>  * @param <E> the type of elements held in this collection
>  */
> ```

翻译如下：

> ```properties
> 
>  * An unbounded priority {@linkplain Queue queue} based on a priority heap.
> 
>  = 是一个基于优先级堆的无界队列
>  
>  * The elements of the priority queue are ordered according to their
>  * {@linkplain Comparable natural ordering}, or by a {@link Comparator}
>  * provided at queue construction time, depending on which constructor is
>  * used. 
>  
>  = 优先级队列中的元素是有序的，排序方式有两种case：第一种是自然排序，前提是元素类中实现了Comparable接口，第二种是根据Comparator来排序，这个Comparator是在队列的构造函数执行时传入的，取决于具体使用哪个构造函数
>  
>  * A priority queue does not permit {@code null} elements.
>  
>   = 优先级队列不允许空元素
>   
>  * A priority queue relying on natural ordering also does not permit
>  * insertion of non-comparable objects (doing so may result in
>  * {@code ClassCastException}). 
>  
>  = 依赖自然排序的优先级对别也不允许插入non-comparable（不可排序的）对象（这样做，会抛异常ClassCastException）
>  
>  * <p>The <em>head</em> of this queue is the <em>least</em> element
>  * with respect to the specified ordering.  
>  
>  = 在具体的排序规则下，队列的首元素是最小元素
>  
>  * If multiple elements are tied for least value, the head is one of those elements -- ties are broken arbitrarily.  
>  
>  = 如果多个元素捆绑为最小值，首元素是其中之一--这种捆绑是任意断开的
>  
>  * The queue retrieval operations {@code poll},
>  * {@code remove}, {@code peek}, and {@code element} access the
>  * element at the head of the queue.
>  
>  = 队列的元素获取操作（poll、remove、peek、element），是从队首获取元素
>  
>  * <p>A priority queue is unbounded, but has an internal
>  * <i>capacity</i> governing the size of an array used to store the
>  * elements on the queue. 
>  
>  = 一个优先级队列是无界的，它使用数组来存储队列元素，而内部有一个值capacity掌控着这个数组的大小
>  
>  * It is always at least as large as the queue
>  * size.  
>  
>  = 这个capacity的值，至少要跟队列的大小一样大
>  
>  * As elements are added to a priority queue, its capacity
>  * grows automatically.  
>  
>  = 当元素被添加到优先级队列，它的capacity自动增大
>  
>  * The details of the growth policy are not
>  * specified.
>  
>  = capacity增长策略的细节没有指明
>  
>  * <p>This class and its iterator implement all of the
>  * <em>optional</em> methods of the {@link Collection} and {@link
>  * Iterator} interfaces.  
>  
>  = 该类和它的迭代器实现了Collection和Iterator接口的所有optional方法
>  
>  
>  The Iterator provided in method {@link
>  * #iterator()} is <em>not</em> guaranteed to traverse the elements of
>  * the priority queue in any particular order. 
>  
>  = iterator()方法返回的迭代器，不能保证以任何特定的顺序来遍历优先级队列中的元素。
>  
>  * If you need ordered
>  * traversal, consider using {@code Arrays.sort(pq.toArray())}.
>  
>  = 如果你需要顺序遍历，考虑使用 Arrays.sort(pq.toArray())这种方式
>  
>  * <p><strong>Note that this implementation is not synchronized.</strong>
>  
>  = 请注意这种实现方式是非同步的
>  
>  * Multiple threads should not access a {@code PriorityQueue}
>  * instance concurrently if any of the threads modifies the queue.
>  
>  = 在多线程场景下，如果任一线程在修改队列，其他线程不该并发的获取PriorityQueue实例
>  
>  * Instead, use the thread-safe {@link
>  * java.util.concurrent.PriorityBlockingQueue} class.
>  
>  = 反之，应使用线程安全的java.util.concurrent.PriorityBlockingQueue类
>  
>  * <p>Implementation note, this implementation provides
>  * O(log(n)) time for the enqueuing and dequeuing methods
>  * ({@code offer}, {@code poll}, {@code remove()} and {@code add});
>  
>  = 这种实现方式下，入队和出队方法（offer, poll, remove, add）时间复杂度都是O(log(n));
>  
>  * linear time for the {@code remove(Object)} and {@code contains(Object)}
>  * methods; and constant time for the retrieval methods
>  * ({@code peek}, {@code element}, and {@code size}).
>  
>  = remove, contains方法的时间复杂度是线性的; peek, element, size方法的时间复杂度是常量
> ```