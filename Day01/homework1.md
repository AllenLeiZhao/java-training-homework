## Q1: List vs Set
List is ordered and allows duplicate values, elements can be accessed by index.
Set is unordered and does not allow duplicate values, no index access.
Common implementations:
List: ArrayList, LinkedList
Set: HashSet, TreeSet, LinkedHashSet
## Q2: LinkedList vs ArrayList
1. ArrayList is consecutive in memory, LinkedList is not.
2. ArrayList can random access elements in O(1) time, LinkedList costs O(n) based on pointers.
3. Insert/delete element in ArrayList cost O(n) in average because it needs to deal with all the following elements, LinkedList only needs O(1) because it only needs to change a pointer.
## Q3: What is Map Interface
Map is an Interface can be used to store key-value pairs. It has multiple implementations like HashMap, TreeMap, HashTable.
1. HashMap is unordered and not thread safe, can contain one null key.
2. LinkedHashMap is sorted by insertion order, not thread safe, can contain one null key.
3. TreeMap is sorted by key, not thread safe, no null key allowed.
4. HashTable: thread safe, but legacy, not recommended
## Q4: How does HashMap work
In java, HashMap is an array of LinkedList. It uses hashCode() to hash keys and index keys to buckets, and uses equals() to compare keys and determine whether they are identical or not. When two different keys are hashed to a same bucket, it uses a LinkedList to hold them. When the length of the LinkedList exceeds 8, it will convert to a red-black tree. The load factor of HashMap is 0.75, when the load reaches this threshold, it will resize.
## Q5: What is hash collision
Hash collision means in a hashmap, when two different keys have the same hashCode, they will be put into a same bucket.
## Q6: What is Collections used for
Collections is a utility class that provides static methods to operate collections, such as sort(), reverse(), shuffle(), max(), min().
## Q7: What is immutable class
Immutable class means once you create an object of this class, you can't change it anymore. For example, String is an immutable class.
## Q8: HashTable vs HashMap vs ConcurrentHashmap
HashTable and ConcurrentHashmap are thread safe, but HashTable is inefficient(due to lock the entire table), so we don't use HashTable anymore. ConcurrentHashmap conducts CAS + Synchronized to ensure thread safe. HashMap is not thread safe.
## Q9: String vs StringBuilder vs StringBuffer
String is immutable, StringBuilder and StringBuffer are mutable. StringBuffer supports multi-threads.
## Q10: why we need to override the hashcode and equals method at the same time
1. If we only override equals(), two equal keys may be hashed to different buckets → put fails
2. If we only override hashCode(), two keys in the same bucket cannot be identified as equal → get returns null
## Q11: play around the common data structure apis (map, set, queue, list), write some practice codes
```java
public class Practice {
    public static void main(String[] args) {
        List<Integer> list1 = new ArrayList<>();
        list1.add(0);
        list1.add(1);
        list1.add(2);
        list1.add(3);
        list1.add(4);
        list1.add(5);
        String[] array = new String[]{"a","b","c","d","e","f"};
        Map<Integer, String> map = new HashMap<>();
        Set<Integer> set = new HashSet<>();
        Queue<Integer> queue = new ArrayDeque<>();
        for(int i = 0; i < array.length; i++) {
            map.put(list1.get(i), array[i]);
            set.add(list1.get(i));
            queue.offer(list1.get(i));
        }
        System.out.println(queue);
        System.out.println(list1);
        System.out.println(array);
        System.out.println(map);
    }
}
```
