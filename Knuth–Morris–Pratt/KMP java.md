```csharp

//28题可以用KMP,也可以用substring, 后者更快。
import java.util.Arrays;
//Solution 1: Strict implmentation of the psedo code
public class solution {

	public int strStr(String haystack, String needle) {
		int h = haystack.length ();
		int m = needle.length();

	//corner cases
		if (m == 0) return 0; //needle == null
		if (h == 0) return -1; //haystack == null
		if (h!=0 && m > h) return -1; // needle.length() > haystack.length()

		int [] prefixTable = getNext(needle);

		int i = 0; 	//index in the pattern

    //find the 1st matching index
		for (int j = 0 ; j < h; j++){
			while (i > 0 & needle.charAt(i) != haystack.charAt(j))
					i = prefixTable[i-1];
			if (needle.charAt(i) == haystack.charAt(j))
				i++;	//when complete match occurs, i still increments 1. 		
			if (i == m)
				return j-m+1;
			}

		return -1; //find no match at the end of haystack
		}

	//calculate KMP Prefix Table		public int[] getNext(String needle) {
			int[] pi = new int[needle.length()];
			pi[0] = 0;

			int i = 0;
			for (int j = 1; j < needle.length(); j++) {
				while (i > 0 && needle.charAt(i) != needle.charAt(j)) 	{
					i = pi[i-1]; }
				if (needle.charAt(i) == needle.charAt(j)) {
					i ++; }
				pi[j] = i;
				}
			System.out.println(Arrays.toString(pi));
			return pi;
		}

//针对28题本身，substring是更快的答案。
//以下解法出自：https://discuss.leetcode.com/topic/9872/share-my-accepted-java-solution
public class Solution {
    public int strStr(String haystack, String needle) {
        int l1 = haystack.length(), l2 = needle.length();
        if (l1 < l2) {
            return -1;
        } else if (l2 == 0) {
            return 0;
        }
        int threshold = l1 - l2;
        for (int i = 0; i <= threshold; ++i) {
            if (haystack.substring(i,i+l2).equals(needle)) {
                return i;
            }
        }
        return -1;
    }
}

public class Solution{
public int strStr(String haystack, String needle) {
    if(needle.equals("")) return 0;
    int L = needle.length();
    for(int i=0; i<=haystack.length()-L; i++)
        if(haystack.substring(i,i+L).equals(needle)) return i;
    return -1;
}
}
```
