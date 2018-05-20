### bit field是在結構體中使用

結構體中可以定義一個叫做bit field的特殊的成員變數。普通的變數，宣告時它的大小一定是以byte為單位。所以，沒法宣告一個只有1 bit的變數，也不能用**short char**來宣告只有4 bit的變數。

但是，結構體中的成員變數，可以像下面這樣宣告bit為單位的變數:

```cpp
struct 結構體tag名稱 {data_type 成員變數名稱: bit數;...};
```

```cpp
struct 結構體tag名稱 變數名稱;
```

要注意的是，data type只能指定unsigned，就是只能處理正數的data type。想宣告並使用一個1 bit的變數，就像下面這樣:

```cpp
struct tag_bit_field{
	unsigned on_off: 1;
};

struct tag_bit_field bit_field;

bit_field.on_off = 1;
```
那麼，就趕快來實驗吧。

**source code**
*bit_field.c*
```cpp
#include <stdio.h>

struct tag_bit_field{
	unsigned on_off: 1;
	unsigned half_byte: 4;
};

int main(){

	struct tag_bit_field bit_field;

	bit_field.on_off = 1;
	bit_field.half_byte = 15;

	printf("bit_field.on_off = %u\n", bit_field.on_off);
	printf("bit_field.half_byte = %u\n", bit_field.half_byte);

	bit_field.on_off = 2;
	bit_field.half_byte = 16;

	printf("bit_field.on_off = %u\n", bit_field.on_off);
	printf("bit_field.half_byte = %u\n", bit_field.half_byte);

	return 0;
}
```
**執行結果:**
```
bit_field.on_off = 1
bit_field.half_byte = 15
bit_field.on_off = 0
bit_field.half_byte = 0
```
1個bit能表示0~1，4個bit能表示0~15，所以第一次的**printf()**可以正常顯示。但是第二次的代入超出了範圍，所以compile時會出現警告，執行時只會顯示0。

0或1代表真偽，如果有許多變數想要代入這種真偽值，就可以用bit field。因為就算是char也需要1 byte的記憶體，假設有八個這種變數，就需要花掉64bits。用bit field的話，只要8 bits就可以了(因為alignment所以可能更大)。

但是，現在的電腦跟server的記憶體都很多，再加上bit field有處理器依存的部份(像是負數的處理)，所以在記憶體有限的環境中才比較推薦用這個吧。

### 把bit field當作是共用體的成員的話

再介紹一個bit field的使用方法吧。bit field單純的就是結構體中的成員變數，也可以跟共用體一起使用。如果把普通變數跟bit field放在一個結構(或共用)體的話，就可以簡單的調查變數所存的數字它的bit列是什麼。

**source code**
*bit_field_2.c*
```cpp
#include <stdio.h>

union tag_byte_union{
	
	unsigned char one_byte;
	struct tag_bit_field{
		unsigned b1: 1;
		unsigned b2: 1;
		unsigned b3: 1;
		unsigned b4: 1;
		unsigned b5: 1;
		unsigned b6: 1;
		unsigned b7: 1;
		unsigned b8: 1;
	} bit_field;
};

int main(){
	union tag_byte_union byte_union;

	byte_union.one_byte = 7;

	printf("%u%u%u%u%u%u%u%u\n",
			byte_union.bit_field.b8,
			byte_union.bit_field.b7,
			byte_union.bit_field.b6,
			byte_union.bit_field.b5,
			byte_union.bit_field.b4,
			byte_union.bit_field.b3,
			byte_union.bit_field.b2,
			byte_union.bit_field.b1);

	return 0;
}
```
**執行結果:**
```
00000111
```
在範例程式中，我們用7代入，結果變成了00000111。我們也可以利用這技巧來顯示**double**型的2進位，不過程式太長了不太現實就是了，程式如下:
```cpp
union tag_double_union{
	double double_val;
	struct tag_bit_field{
		unsigned b1: 1;
		unsigned b2: 1;
		unsigned b3: 1;
		unsigned b4: 1;
		unsigned b5: 1;
		unsigned b6: 1;
		unsigned b7: 1;
		unsigned b8: 1;
        unsigned b9: 1;
		unsigned b10: 1;
		unsigned b11: 1;
		unsigned b12: 1;
		unsigned b13: 1;
		unsigned b14: 1;
		unsigned b15: 1;
		unsigned b16: 1;
        unsigned b17: 1;
		unsigned b18: 1;
		unsigned b19: 1;
		unsigned b20: 1;
		unsigned b21: 1;
		unsigned b22: 1;
		unsigned b23: 1;
		unsigned b24: 1;
        unsigned b25: 1;
		unsigned b26: 1;
		unsigned b27: 1;
		unsigned b28: 1;
		unsigned b29: 1;
		unsigned b30: 1;
		unsigned b31: 1;
		unsigned b32: 1;
        unsigned b33: 1;
		unsigned b34: 1;
		unsigned b35: 1;
		unsigned b36: 1;
		unsigned b37: 1;
		unsigned b38: 1;
		unsigned b39: 1;
		unsigned b40: 1;
        unsigned b41: 1;
		unsigned b42: 1;
		unsigned b43: 1;
		unsigned b44: 1;
		unsigned b45: 1;
		unsigned b46: 1;
		unsigned b47: 1;
		unsigned b48: 1;
        unsigned b49: 1;
		unsigned b50: 1;
		unsigned b51: 1;
		unsigned b52: 1;
		unsigned b53: 1;
		unsigned b54: 1;
		unsigned b55: 1;
		unsigned b56: 1;
        unsigned b57: 1;
		unsigned b58: 1;
		unsigned b59: 1;
		unsigned b60: 1;
		unsigned b61: 1;
		unsigned b62: 1;
		unsigned b63: 1;
		unsigned b64: 1;
	} bit_field;
};

printf("%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u"
"%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u%u\n",
			double_union.bit_field.b64,
			double_union.bit_field.b63,
			double_union.bit_field.b62,
			double_union.bit_field.b61,
			double_union.bit_field.b60,
			double_union.bit_field.b59,
			double_union.bit_field.b58,
			double_union.bit_field.b57,
            double_union.bit_field.b56,
			double_union.bit_field.b55,
			double_union.bit_field.b54,
			double_union.bit_field.b53,
			double_union.bit_field.b52,
			double_union.bit_field.b51,
			double_union.bit_field.b50,
			double_union.bit_field.b49,
			double_union.bit_field.b48,
			double_union.bit_field.b47,
			double_union.bit_field.b46,
			double_union.bit_field.b45,
			double_union.bit_field.b44,
			double_union.bit_field.b43,
			double_union.bit_field.b42,
			double_union.bit_field.b41,
            double_union.bit_field.b40,
			double_union.bit_field.b39,
			double_union.bit_field.b38,
			double_union.bit_field.b37,
			double_union.bit_field.b36,
			double_union.bit_field.b35,
			double_union.bit_field.b34,
			double_union.bit_field.b33,
			double_union.bit_field.b32,
			double_union.bit_field.b31,
			double_union.bit_field.b30,
			double_union.bit_field.b29,
			double_union.bit_field.b28,
			double_union.bit_field.b27,
			double_union.bit_field.b26,
			double_union.bit_field.b25,
            double_union.bit_field.b24,
			double_union.bit_field.b23,
			double_union.bit_field.b22,
			double_union.bit_field.b21,
			double_union.bit_field.b20,
			double_union.bit_field.b19,
			double_union.bit_field.b18,
			double_union.bit_field.b17,
			double_union.bit_field.b16,
			double_union.bit_field.b15,
			double_union.bit_field.b14,
			double_union.bit_field.b13,
			double_union.bit_field.b12,
			double_union.bit_field.b11,
			double_union.bit_field.b10,
			double_union.bit_field.b9,
            double_union.bit_field.b8,
			double_union.bit_field.b7,
			double_union.bit_field.b6,
			double_union.bit_field.b5,
			double_union.bit_field.b4,
			double_union.bit_field.b3,
			double_union.bit_field.b2,
			double_union.bit_field.b1
            );
```