```csharp
import static org.junit.Assert.*;

import org.junit.Test;

public class solutionTest {
	
	solution result = new solution();
	
	@Test
	public void testStr(){
		int test = result.strStr("ecdabcdabeab", "abcdabe");
		assertEquals (3, test); 
	}

	@Test
	public void testGetNext() {
		int[] test1 = result.getNext("abcdabcb");
		assertArrayEquals (new int[]{0, 0, 0, 0, 1, 2, 3, 0},test1);
		int[] test2 = result.getNext("abcdabd");
		assertArrayEquals (new int[]{0, 0, 0, 0, 1, 2,0},test2);
		int[] test3 = result.getNext("aab");
		assertArrayEquals (new int[]{0, 1, 0},test3);
		int [] test4 = result.getNext("bbabba");
		assertArrayEquals(new int[] {0, 1, 0, 1, 2, 3}, test4);
		
	}

}
```