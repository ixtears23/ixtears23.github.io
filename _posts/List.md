~~~java

package collections.list;

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;

public class ListSpeedTest {

	public static void main(String[] args) {

		ArrayList<String> al = new ArrayList<>(2000000);
		LinkedList<String> ll = new LinkedList<>();

		System.out.println("= 순차적으로 추가하기 =");
		System.out.println("ArrayList :" + add1(al));
		System.out.println("LinkedList :" + add1(ll));
		System.out.println();
		System.out.println("= 중간에 추가하기 =");
		System.out.println("ArrayList :" + add2(al));
		System.out.println("LinkedList :" + add2(ll));
		System.out.println();
		System.out.println("= 중간에서 삭제하기 =");
		System.out.println("ArrayList :" + remove2(al));
		System.out.println("LinkedList :" + remove2(ll));
		System.out.println();
		System.out.println("= 순차적으로 삭제하기 =");
		System.out.println("ArrayList :" + remove1(al));
		System.out.println("LinkedList :" + remove1(ll));
		System.out.println();
	}

	public static long add1(List<String> al) {
		long start = System.currentTimeMillis();
		for(int i = 0; i < 1000000; i++) {
			al.add(i + "");
		}
		long end = System.currentTimeMillis();
		return end - start;
	}

	public static long add2(List<String> list) {
		long start = System.currentTimeMillis();
		for(int i = 0; i < 10000; i++) {
			list.add(500, "X");
		}
		long end = System.currentTimeMillis();
		return end - start;
	}

	public static long remove1(List<String> list) {
		long start = System.currentTimeMillis();
		for(int i = list.size() - 1; i >= 0; i--) {
			list.remove(i);
		}
		long end = System.currentTimeMillis();
		return end - start;
	}

	public static long remove2(List<String> list) {
		long start = System.currentTimeMillis();
		for(int i = 0; i < 10000; i++) {
			list.remove(i);
		}
		long end = System.currentTimeMillis();
		return end - start;
	}
}

~~~

### output1

~~~
= 순차적으로 추가하기 =
ArrayList :1638
LinkedList :1353

= 중간에 추가하기 =
ArrayList :9071
LinkedList :27

= 중간에서 삭제하기 =
ArrayList :9597
LinkedList :404

= 순차적으로 삭제하기 =
ArrayList :16
LinkedList :45
~~~

### output2

~~~
= 순차적으로 추가하기 =
ArrayList :1464
LinkedList :1496

= 중간에 추가하기 =
ArrayList :9177
LinkedList :26

= 중간에서 삭제하기 =
ArrayList :8997
LinkedList :258

= 순차적으로 삭제하기 =
ArrayList :16
LinkedList :42
~~~

---

### 결론

- 순차적으로 추가/삭제하는 경우에는 ArrayList가 LinkedList 보다 빠르다.  
- 중간 데이터를 추가/삭제하는 경우에는 LinkedList가 ArrayList 보다 빠르다.  


~~~java

package collections.list;

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;

public class ArrayListLinkedListTest2 {

	public static void main(String[] args) {

		ArrayList<Object> al = new ArrayList<>(1000000);
		LinkedList<Object> ll = new LinkedList<>();
		add(al);
		add(ll);

		System.out.println("= 접근시간테스트 =");
		System.out.println("ArrayList :" + access(al));
		System.out.println("LinkedList :" + access(ll));

	}

	public static void add(List<Object> list) {
		for(int i = 0; i < 100000; i++) list.add(i + "");
	}

	public static long access(List<?> list) {
		long start = System.currentTimeMillis();
		for(int i = 0; i < 10000; i++) list.get(i);
		long end = System.currentTimeMillis();
		return end - start;
	}
}

~~~

### output1

~~~
= 접근시간테스트 =
ArrayList :1
LinkedList :323
~~~

### output2

~~~
= 접근시간테스트 =
ArrayList :1
LinkedList :362
~~~

---

### 결론

- LinkedList는 저장해야하는 데이터의 개수가 많아질수록 데이터를 읽어 오는 시간, 즉 접근시간이 길어진다는 단점이 있다.  

- 데이터의 개수가 변하지 않는 경우라면 ArrayList  
- 데이터 개수의 변경이 잦다면 LinkedList  

|컬렉션|읽기(접근시간)|추가/삭제|비고|
|:---|:---:|:---:|:---|
|ArrayList|빠르다|느리다|순차적인 추가삭제는 더 빠름.  비효율적인 메모리사용.
|LinkedList|느리다|빠르다|데이터가 많을수록 접근성이 떨어짐.|
