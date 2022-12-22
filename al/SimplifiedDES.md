# Code
```java

import java.io.BufferedReader;
import java.io.InputStreamReader;

class SDES {
	public int K1, K2;
	public static final int P10[] = { 3, 5, 2, 7, 4, 10, 1, 9, 8, 6 };
	public static final int P10max = 10;
	public static final int P8[] = { 6, 3, 7, 4, 8, 5, 10, 9 };
	public static final int P8max = 10;
	public static final int P4[] = { 2, 4, 3, 1 };
	public static final int P4max = 4;
	public static final int IP[] = { 2, 6, 3, 1, 4, 8, 5, 7 };
	public static final int IPmax = 8;
	public static final int IPI[] = { 4, 1, 3, 5, 7, 2, 8, 6 };
	public static final int IPImax = 8;
	public static final int EP[] = { 4, 1, 2, 3, 2, 3, 4, 1 };
	public static final int EPmax = 4;
	public static final int S0[][] = { { 1, 0, 3, 2 }, { 3, 2, 1, 0 }, { 0, 2, 1,
			3 }, { 3, 1, 3, 2 } };
	public static final int S1[][] = { { 0, 1, 2, 3 }, { 2, 0, 1, 3 }, { 3, 0, 1,
			2 }, { 2, 1, 0, 3 } };

	public static int permute(int x, int p[], int pmax) {
		int y = 0;
		for (int i = 0; i < p.length; ++i) {
			y <<= 1;
			y |= (x >> (pmax - p[i])) & 1;
		}
		return y;
	}

	public static int F(int R, int K) {
		int t = permute(R, EP, EPmax) ^ K;
		int t0 = (t >> 4) & 0xF;
		int t1 = t & 0xF;
		t0 = S0[((t0 & 0x8) >> 2) | (t0 & 1)][(t0 >> 1) & 0x3];
		t1 = S1[((t1 & 0x8) >> 2) | (t1 & 1)][(t1 >> 1) & 0x3];
		t = permute((t0 << 2) | t1, P4, P4max);
		return t;

	}

	public static int fK(int m, int K) {
		int L = (m >> 4) & 0xF;
		int R = m & 0xF;
		return ((L ^ F(R, K)) << 4) | R;
	}

	public static int SW(int x) {
		return ((x & 0xF) << 4) | ((x >> 4) & 0xF);
	}

	public byte encrypt(int m) {
		System.out.println("\nEncryption Process Starts........\n\n");
		m = permute(m, IP, IPmax);
		System.out.print("\nAfter Permutation : ");
		printData(m, 8);
		m = fK(m, K1);
		System.out.print("\nbefore Swap : ");
		printData(m, 8);
		m = SW(m);
		System.out.print("\nAfter Swap : ");
		printData(m, 8);
		m = fK(m, K2);
		System.out.print("\nbefore IP inverse : ");
		printData(m, 8);
		m = permute(m, IPI, IPImax);
		return (byte) m;

	}

	public byte decrypt(int m)

	{
		System.out.println("\nDecryption Process Starts........\n\n");
		printData(m, 8);
		m = permute(m, IP, IPmax);
		System.out.print("\nAfter Permutation : ");
		printData(m, 8);
		m = fK(m, K2);
		System.out.print("\nbefore Swap : ");
		printData(m, 8);
		m = SW(m);
		System.out.print("\nAfter Swap : ");
		printData(m, 8);
		m = fK(m, K1);
		System.out.print("\nBefore Extraction Permutation : ");
		printData(m, 4);
		m = permute(m, IPI, IPImax);
		System.out.print("\nAfter Extraction Permutation : ");
		printData(m, 8);
		return (byte) m;

	}

	public static void printData(int x, int n) {
		int mask = 1 << (n - 1);
		while (mask > 0) {
			System.out.print(((x & mask) == 0) ? '0' : '1');
			mask >>= 1;
		}
	}

	public SDES(int K) {
		K = permute(K, P10, P10max);
		int t1 = (K >> 5) & 0x1F;
		int t2 = K & 0x1F;
		t1 = ((t1 & 0xF) << 1) | ((t1 & 0x10) >> 4);
		t2 = ((t2 & 0xF) << 1) | ((t2 & 0x10) >> 4);
		K1 = permute((t1 << 5) | t2, P8, P8max);
		t1 = ((t1 & 0x7) << 2) | ((t1 & 0x18) >> 3);
		t2 = ((t2 & 0x7) << 2) | ((t2 & 0x18) >> 3);
		K2 = permute((t1 << 5) | t2, P8, P8max);

	}

}

// Main operations
public class SimplifiedDES {

	public static void main(String args[]) throws Exception {
		BufferedReader inp = new BufferedReader(new InputStreamReader(System.in));
		System.out.println("Enter the 10 Bit Key :");
		int K = Integer.parseInt(inp.readLine(), 2);
		SDES A = new SDES(K);
		System.out.println("Enter the 8 Bit message To be Encrypt  : ");
		int m = Integer.parseInt(inp.readLine(), 2);
		System.out.print("\nKey K1: ");
		SDES.printData(A.K1, 8);
		System.out.print("\nKey K2: ");
		SDES.printData(A.K2, 8);
		m = A.encrypt(m);
		System.out.print("\nEncrypted Message: ");
		SDES.printData(m, 8);
		m = A.decrypt(m);
		System.out.print("\nDecrypted Message: ");
		SDES.printData(m, 8);
	}
}


```


# Output

```shell

Enter the 10 Bit Key :
1011011010
Enter the 8 Bit message To be Encrypt  : 
10110110

Key K1: 11110101
Key K2: 01100011
Encryption Process Starts........



After Permutation : 01111001
before Swap : 00001001
After Swap : 10010000
before IP inverse : 10000000
Encrypted Message: 01000000
Decryption Process Starts........


01000000
After Permutation : 10000000
before Swap : 10010000
After Swap : 00001001
Before Extraction Permutation : 1001
After Extraction Permutation : 10110110
Decrypted Message: 10110110

```
/*package whatever //do not write package name here */

import java.io.*;

public class GFG {
	// int key[]= {0,0,1,0,0,1,0,1,1,1};
	int key[] = {
		1, 0, 1, 0, 0, 0, 0, 0, 1, 0
	}; // extra example for checking purpose
	int P10[] = { 3, 5, 2, 7, 4, 10, 1, 9, 8, 6 };
	int P8[] = { 6, 3, 7, 4, 8, 5, 10, 9 };

	int key1[] = new int[8];
	int key2[] = new int[8];

	int[] IP = { 2, 6, 3, 1, 4, 8, 5, 7 };
	int[] EP = { 4, 1, 2, 3, 2, 3, 4, 1 };
	int[] P4 = { 2, 4, 3, 1 };
	int[] IP_inv = { 4, 1, 3, 5, 7, 2, 8, 6 };

	int[][] S0 = { { 1, 0, 3, 2 },
				{ 3, 2, 1, 0 },
				{ 0, 2, 1, 3 },
				{ 3, 1, 3, 2 } };
	int[][] S1 = { { 0, 1, 2, 3 },
				{ 2, 0, 1, 3 },
				{ 3, 0, 1, 0 },
				{ 2, 1, 0, 3 } };

	// this function basically generates the key(key1 and
	//key2)	 using P10 and P8 with (1 and 2)left shifts

	void key_generation()
	{
		int key_[] = new int[10];

		for (int i = 0; i < 10; i++) {
			key_[i] = key[P10[i] - 1];
		}

		int Ls[] = new int[5];
		int Rs[] = new int[5];

		for (int i = 0; i < 5; i++) {
			Ls[i] = key_[i];
			Rs[i] = key_[i + 5];
		}

		int[] Ls_1 = shift(Ls, 1);
		int[] Rs_1 = shift(Rs, 1);

		for (int i = 0; i < 5; i++) {
			key_[i] = Ls_1[i];
			key_[i + 5] = Rs_1[i];
		}

		for (int i = 0; i < 8; i++) {
			key1[i] = key_[P8[i] - 1];
		}

		int[] Ls_2 = shift(Ls, 2);
		int[] Rs_2 = shift(Rs, 2);

		for (int i = 0; i < 5; i++) {
			key_[i] = Ls_2[i];
			key_[i + 5] = Rs_2[i];
		}

		for (int i = 0; i < 8; i++) {
			key2[i] = key_[P8[i] - 1];
		}

		System.out.println("Your Key-1 :");

		for (int i = 0; i < 8; i++)
			System.out.print(key1[i] + " ");

		System.out.println();
		System.out.println("Your Key-2 :");

		for (int i = 0; i < 8; i++)
			System.out.print(key2[i] + " ");
	}

	// this function is use full for shifting(circular) the
	//array n position towards left

	int[] shift(int[] ar, int n)
	{
		while (n > 0) {
			int temp = ar[0];
			for (int i = 0; i < ar.length - 1; i++) {
				ar[i] = ar[i + 1];
			}
			ar[ar.length - 1] = temp;
			n--;
		}
		return ar;
	}

	// this is main encryption function takes plain text as
	//input	 uses another functions and returns the array of
	//cipher text

	int[] encryption(int[] plaintext)
	{
		int[] arr = new int[8];

		for (int i = 0; i < 8; i++) {
			arr[i] = plaintext[IP[i] - 1];
		}
		int[] arr1 = function_(arr, key1);

		int[] after_swap = swap(arr1, arr1.length / 2);

		int[] arr2 = function_(after_swap, key2);

		int[] ciphertext = new int[8];

		for (int i = 0; i < 8; i++) {
			ciphertext[i] = arr2[IP_inv[i] - 1];
		}

		return ciphertext;
	}

	// decimal to binary string 0-3

	String binary_(int val)
	{
		if (val == 0)
			return "00";
		else if (val == 1)
			return "01";
		else if (val == 2)
			return "10";
		else
			return "11";
	}

	// this function is doing core things like expansion
	// then xor with desired key then S0 and S1
	//substitution	 P4 permutation and again xor	 we have used
	//this function 2 times(key-1 and key-2) during
	//encryption and	 2 times(key-2 and key-1) during
	//decryption

	int[] function_(int[] ar, int[] key_)
	{

		int[] l = new int[4];
		int[] r = new int[4];

		for (int i = 0; i < 4; i++) {
			l[i] = ar[i];
			r[i] = ar[i + 4];
		}

		int[] ep = new int[8];

		for (int i = 0; i < 8; i++) {
			ep[i] = r[EP[i] - 1];
		}

		for (int i = 0; i < 8; i++) {
			ar[i] = key_[i] ^ ep[i];
		}

		int[] l_1 = new int[4];
		int[] r_1 = new int[4];

		for (int i = 0; i < 4; i++) {
			l_1[i] = ar[i];
			r_1[i] = ar[i + 4];
		}

		int row, col, val;

		row = Integer.parseInt("" + l_1[0] + l_1[3], 2);
		col = Integer.parseInt("" + l_1[1] + l_1[2], 2);
		val = S0[row][col];
		String str_l = binary_(val);

		row = Integer.parseInt("" + r_1[0] + r_1[3], 2);
		col = Integer.parseInt("" + r_1[1] + r_1[2], 2);
		val = S1[row][col];
		String str_r = binary_(val);

		int[] r_ = new int[4];
		for (int i = 0; i < 2; i++) {
			char c1 = str_l.charAt(i);
			char c2 = str_r.charAt(i);
			r_[i] = Character.getNumericValue(c1);
			r_[i + 2] = Character.getNumericValue(c2);
		}
		int[] r_p4 = new int[4];
		for (int i = 0; i < 4; i++) {
			r_p4[i] = r_[P4[i] - 1];
		}

		for (int i = 0; i < 4; i++) {
			l[i] = l[i] ^ r_p4[i];
		}

		int[] output = new int[8];
		for (int i = 0; i < 4; i++) {
			output[i] = l[i];
			output[i + 4] = r[i];
		}
		return output;
	}

	// this function swaps the nibble of size n(4)

	int[] swap(int[] array, int n)
	{
		int[] l = new int[n];
		int[] r = new int[n];

		for (int i = 0; i < n; i++) {
			l[i] = array[i];
			r[i] = array[i + n];
		}

		int[] output = new int[2 * n];
		for (int i = 0; i < n; i++) {
			output[i] = r[i];
			output[i + n] = l[i];
		}

		return output;
	}

	// this is main decryption function
	// here we have used all previously defined function
	// it takes cipher text as input and returns the array
	//of	 decrypted text

	int[] decryption(int[] ar)
	{
		int[] arr = new int[8];

		for (int i = 0; i < 8; i++) {
			arr[i] = ar[IP[i] - 1];
		}

		int[] arr1 = function_(arr, key2);

		int[] after_swap = swap(arr1, arr1.length / 2);

		int[] arr2 = function_(after_swap, key1);

		int[] decrypted = new int[8];

		for (int i = 0; i < 8; i++) {
			decrypted[i] = arr2[IP_inv[i] - 1];
		}

		return decrypted;
	}

	public static void main(String[] args)
	{

		GFG obj = new GFG();

		obj.key_generation(); // call to key generation
							// function

		// int []plaintext= {1,0,1,0,0,1,0,1};
		int[] plaintext = {
			1, 0, 0, 1, 0, 1, 1, 1
		}; // extra example for checking purpose

		System.out.println();
		System.out.println("Your plain Text is :");
		for (int i = 0; i < 8; i++) // printing the
									// plaintext
			System.out.print(plaintext[i] + " ");

		int[] ciphertext = obj.encryption(plaintext);

		System.out.println();
		System.out.println(
			"Your cipher Text is :"); // printing the cipher
									// text
		for (int i = 0; i < 8; i++)
			System.out.print(ciphertext[i] + " ");

		int[] decrypted = obj.decryption(ciphertext);

		System.out.println();
		System.out.println(
			"Your decrypted Text is :"); // printing the
										// decrypted text
		for (int i = 0; i < 8; i++)
			System.out.print(decrypted[i] + " ");
	}
}


