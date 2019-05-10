# CopyOnWriteArrayList
CopyOnWriteArrayList是ArrayList的一个线程安全的变体，所有可变操作都是通过对底层数组进行一次新的复制来完成的。

这种复制操作一般都需要很大的开销，但是当遍历操作的数量大大超过可变操作的数量时，这种方法可能比其他替代方法更 有效。在不能或不想进行同步遍历，但又需要从并发线程中排除冲突时，它也很有用。“快照”风格的迭代器方法在创建迭代器时使用了对数组状态的引用。此数组在迭代器的生存期内不会更改，因此不可能发生冲突，并且迭代器保证不会抛出ConcurrentModificationException。创建迭代器以后，迭代器就不会反映列表的添加、移除或者更改。在迭代器上进行的元素更改操作（remove、set和add）不受支持。这些方法将抛出UnsupportedOperationException。允许使用所有元素，包括null。

CopyOnWriteArrayList add(E) 和remove(int index)都是对新的数组进行修改和新增。所以在多线程操作时不会出现java.util.ConcurrentModificationException错误。

CopyOnWriteArrayList适合使用在读操作远远大于写操作的场景里，比如缓存。发生修改时候做copy，新老版本分离，保证读的高性能，适用于以读为主的情况。

通常情况下我们的高并发都发生在“多读少写”的情况，因此如果能够实现一种更优秀的算法这对生产环境还是很有好处的。ReadWriteLock当然是一种实现。CopyOnWriteArrayList/CopyOnWriteArraySet确实另外一种思路。

CopyOnWriteArrayList/CopyOnWriteArraySet 的基本思想是一旦对容器有修改，那么就“复制”一份新的集合，在新的集合上修改，然后将新集合复制给旧的引用。当然了这部分少不了要加锁。显然对于 CopyOnWriteArrayList/CopyOnWriteArraySet来说最大的好处就是“读”操作不需要锁了。

基于数组的实现。

为了保证无锁的读操作能够看到写操作的变化，因此数组array是volatile类型的。

get/indexOf/iterator等操作都是无锁的，同时也可以看到所操作的都是某一时刻array的镜像（这得益于数组是不可变化的）

add/set/remove/clear等元素变化的都是需要加锁的，这里使用的是ReentrantLock。

