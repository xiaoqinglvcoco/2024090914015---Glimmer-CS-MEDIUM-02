# 2024090914015---Glimmer-CS-MEDIUM-02
微光招新题cs5(没写完只能先交了www)
## 课前准备
## 任务1
1. 为什么会出现这样的现象？
在计算机中，尤其是使用二进制浮点数表示时，像0.1和0.2这样的十进制小数并不能被精确表示。这是因为二进制浮点数系统无法精确表示所有十进制小数，就像十进制系统无法精确表示所有二进制小数（如1/3）一样。
当声明并初始化float a = 0.1;和float b = 0.2;时，这两个值在内存中被近似为最接近的二进制浮点数。由于二进制浮点数的表示方式，这些近似值可能并不完全等于它们十进制表示的精确值。当执行float c = a + b;时，你实际上是在将两个近似值相加。这个相加的结果也可能是一个近似值，它可能并不完全等于十进制结果0.3。（这个是自己的理解）
（下面的内容为《深入理解计算机系统》的原话：假定我们仅考虑有限长度的编码，那么十进制表示法不能准确地表达像三分之一和七分之五这样的数。类似，小数的二进制表示法只能表示那些能够被写成 x*2的y次幂的数。其他的值只能够被近似地表示。例如，数字一可以用十进制小数 0.20 精确表示。不过，我们并不能把它准确地表示为一个二进制小数，我们只能近似地表示它，增加二进制表示的长度可以提高表示的精度）
[![pAwknHA.png](https://s21.ax1x.com/2024/10/24/pAwknHA.png)](https://imgse.com/i/pAwknHA)
2.  + 该如何获得精确值呢？
        1. 使用高精度库：
        Java 中的 BigDecimal：BigDecimal 提供了在任意精度上执行算术运算的能力。
        Python 中的 decimal 模块：提供了类似 BigDecimal 的功能。
        C语言 中的高精度库，如 GMP (GNU Multiple Precision Arithmetic Library)：虽然标准C库不支持高精度浮点数，但你可以使用GMP库来进行高精度算术运算。
        2. 整数运算代替：如果你只处理固定的小数位数（如财务计算中的货币），可以将小数部分转换为整数进行计算，最后再转换回小数。例如，将所有值乘以100（假设只处理两位小数），进行计算后再除以100。
        3. 调整输出格式：（但是实际上最好不要这样做，毕竟只是看起来好看而已）如果你不需要非常高的精度，但希望输出看起来更“干净”，可以调整输出格式，例如使用 printf 的格式化选项来限制小数位数。
    + 二进制中的移位运算：移位运算是二进制操作中的一种基本运算，主要用于将二进制数的位向左或向右移动[![pAwEMSf.png](https://s21.ax1x.com/2024/10/24/pAwEMSf.png)](https://imgse.com/i/pAwEMSf)
       1. 左移运算 (<<)：左移运算将二进制数的每一位向左移动指定的位数。高位被丢弃，低位用0填充。 左移一位相当于将该数乘以2。因此，左移n位相当于将该数乘以2^n。
       2. 右移运算将二进制数的每一位向右移动指定的位数。低位被丢弃，高位的填充方式取决于右移运算的类型。右移一位相当于将该数除以2。因此，右移n位相当于将该数除以2^n（向下取整）
          1. 逻辑右移：逻辑右移在高位用0填充。假设有一个8位的二进制数 00001010，将其逻辑右移2位：00001010 >> 2 = 00000010
          2. 算术右移：算术右移在高位用符号位（最高位）填充，以保持数的符号不变。假设有一个8位的二进制数 10001010（负数），将其算术右移2位：10001010 >> 2 = 11100010
        3. 循环移位：循环移位（也称为旋转移位）将二进制数的每一位向左或向右移动指定的位数，但丢弃的位会被重新填回另一端。假设有一个8位的二进制数 00001010，将其循环右移2位：00001010 ROR 2 = 10000010
           4. 循环左移 :将二进制数的每一位向左移动指定的位数，丢弃的高位会被填到低位
           5. 循环右移:将二进制数的每一位向右移动指定的位数，丢弃的低位会被填到高位。
3. 二进制与十进制的相互转化
   1. 运行结果：[![pAwanSK.png](https://s21.ax1x.com/2024/10/25/pAwanSK.png)](https://imgse.com/i/pAwanSK)
   2. 代码 
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <math.h>
// 十进制转二进制  
void DtoB(double decimal, char *result) {  
    char intpart[100] = "",fracpart[100] = "";  
    int int_part = (int)decimal;
    double frac_part = decimal - int_part;
    // 转换整数部分
    if (int_part == 0) {
        strcat(intpart, "0");
    } else {
        while (int_part > 0) {  
            char remainder = int_part % 2 + '0';
            strcat(intpart, &remainder);
            int_part /= 2;
        }
        // 反转字符串
        int len = strlen(intpart);
        for (int i = 0; i < len / 2; i++) {
            char temp = intpart[i];
            intpart[i] = intpart[len - i - 1];
            intpart[len - i - 1] = temp;
        }
    }
    // 转换小数部分  
    int count = 0;  
    while (frac_part > 0 && count < 50) { // 限制小数部分位数为50位以防无限循环  
        frac_part *= 2;//乘2
        int digit = (int)frac_part;//取整数部分
        char c_digit = digit + '0';
        strcat(fracpart, &c_digit);//放到小数部分
        frac_part -= digit;//取剩下的小数部分作为下一次计算的初始值
        count++;//防止无限循环而爆掉
    }
    // 合并结果  
    if (strlen(fracpart) > 0) {  
        sprintf(result, "%s.%s", intpart, fracpart);  
    } else {  
        sprintf(result, "%s", intpart);  
    }  
}
// 二进制转十进制
void BtoD(const char *binary, char *result) {
    char intpart[100] = "",fracpart[100] = "";
    int point_index = -1;
    // 查找小数点的位置  
    for (int i = 0; i < strlen(binary); i++) {  
        if (binary[i] == '.') {  
            point_index = i;  
            break;  
        }  
    }
    // 提取整数部分和小数部分  
    if (point_index == -1) {//找不到小数点
        strncpy(intpart, binary, strlen(binary));  
    } else {
        strncpy(intpart, binary, point_index);//储存整数部分
        strncpy(fracpart, binary + point_index + 1, strlen(binary) - point_index - 1);//储存小数部分
    }
    // 转换整数部分
    int int_value = 0;
    for (int i = strlen(intpart) - 1; i >= 0; i--) {
        int_value += (intpart[i] - '0') * pow(2, strlen(intpart) - 1 - i);//当前位乘2的i-1次幂
    }
    // 转换小数部分
    double frac_value = 0.0;
    int fraction_length = strlen(fracpart);  
    for (int i = 0; i < fraction_length; i++) {  
        frac_value += (fracpart[i] - '0') / pow(2, i + 1);//当前位乘2的-（i-1）次幂也就是除2的i-1次幂  
    }
    // 合并结果  
    double decimal_value = int_value + frac_value;  
    sprintf(result, "%lf", decimal_value);
}
// 判断是否是二进制数  
int is_binary(const char *str) {  
    return strlen(str) > 1 && str[strlen(str) - 1] == 'B';  
} 
// 判断是否是十进制数  
int is_decimal(const char *str) {  
    return strlen(str) > 1 && str[strlen(str) - 1] == 'D';  
} 
char* removezeros(const char* str) {  
    // 查找小数点的位置
    const char* point = strchr(str, '.');  
    if (point == NULL) {
        // 如果没有小数点，直接复制字符串
        size_t len = strlen(str);
        char* result = (char*)malloc(len + 1);
        strcpy(result, str);
        return result;
    }
    size_t len1 = point - str,len2 = strlen(point + 1);// 计算整数部分和小数部分的长度
    char* result = (char*)malloc(len1 + len2 + 2);
    strncpy(result, str, len1);// 复制整数部分
    result[len1] = '.';  
    size_t j = len1 + 1; // 指向小数部分的第一个字符
    // 复制小数部分，同时去除后导零
    int temp=0;
    for (size_t i = 1; i <= len2; i++) {
        if (point[i] != '0') {
            temp=i; // 如果当前数字不是0就记录下他的位置
        }
    }
    for(int i=1;i<=temp;i++) {
        result[j++]=point[i];
    }
    // 如果小数部分全是0（且不是紧跟在另一个0或小数点后），则移除小数点  
    if (j == len1 + 1 && (len2 == 0 || (len2 > 0 && point[1] == '0'))) {  
        // 但这里要注意，如果整数部分也是0（即整个数是0.000...），则应该保留一个'0'  
        if (len1 == 0 || (len1 == 1 && str[0] == '0')) {  
            result[0] = '0';  
            result[1] = '\0';  
        } else {  
            result[len1] = '\0'; // 只保留整数部分  
        }  
    } else {  
        result[j] = '\0'; // 添加字符串结束符  
    }
    return result;  
}
int main() {  
    char input[100],result[100],numstr[100];
    scanf("%s", input);
    // 判断是十进制还是二进制  
    if (is_decimal(input)) {
        // 提取数字部分
        strncpy(numstr, input, strlen(input) - 1);
        numstr[strlen(input) - 1] = '\0';
        // 转换为二进制
        DtoB(atof(numstr), result);
        strcpy(result,removezeros(result));
        printf("十进制数 %s 转换为二进制数是：%sB\n", input, result);  
    } else if (is_binary(input)) {
        // 提取数字部分
        strncpy(numstr, input, strlen(input) - 1);
        numstr[strlen(input) - 1] = '\0';
        // 转换为十进制
        BtoD(numstr, result);
        strcpy(result,removezeros(result));
        printf("二进制数 %s 转换为十进制数是：%sD\n", input, result);  
    }
    return 0;  
}
```
## 任务2
### step1(程序目前还有问题但是来不及调了唉，只能先交个半成品了)
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <math.h>
typedef struct {  
    int sign;       // 符号位：1表示正数，-1表示负数  
    char integer[32];  // 整数部分  
    char fraction[32]; // 小数部分
} PointFixedNum;
// 初始化函数，将double类型的值转换为PointFixedNum表示  
void init(PointFixedNum *num, char input[]) {  
        // 查找小数点的位置
    const char* point = strchr(input, '.');  
    if (point == NULL) {
        // 如果没有小数点，直接复制字符串
        strcpy(num->integer, input);
        num->fraction[0]='0';
        num->fraction[1]='\0';
        return;
    }
    size_t len1 = point - input,len2 = strlen(point + 1);// 计算整数部分和小数部分的长度
    strncpy(num->integer, input, len1);// 复制整数部分
    // 复制小数部分，同时去除后导零
    int temp=0;
    for (size_t i = 0; i <= len2; i++) {
        if (point[i] != '0') {
            temp=i; // 如果当前数字不是0就记录下他的位置
        }
    }
    int j=0;
    for(int i=0;i<=temp;i++) {
        num->fraction[j++]=point[i];
    }
}
int is_binary(const char *str) {  
    return strlen(str) > 1 && str[strlen(str) - 1] == 'B';  
} 
// 判断是否是十进制数  
int is_decimal(const char *str) {  
    return strlen(str) > 1 && str[strlen(str) - 1] == 'D';  
}
// 二进制转十进制
void BtoD(const char *binary, char *result) {
    char intpart[100] = "",fracpart[100] = "";
    int point_index = -1;
    // 查找小数点的位置  
    for (int i = 0; i < strlen(binary); i++) {  
        if (binary[i] == '.') {  
            point_index = i;  
            break;  
        }  
    }
    // 提取整数部分和小数部分  
    if (point_index == -1) {//找不到小数点
        strncpy(intpart, binary, strlen(binary));  
    } else {
        strncpy(intpart, binary, point_index);//储存整数部分
        strncpy(fracpart, binary + point_index + 1, strlen(binary) - point_index - 1);//储存小数部分
    }
    // 转换整数部分
    int int_value = 0;
    for (int i = strlen(intpart) - 1; i >= 0; i--) {
        int_value += (intpart[i] - '0') * pow(2, strlen(intpart) - 1 - i);//当前位乘2的i-1次幂
    }
    // 转换小数部分
    double frac_value = 0.0;
    int fraction_length = strlen(fracpart);  
    for (int i = 0; i < fraction_length; i++) {  
        frac_value += (fracpart[i] - '0') / pow(2, i + 1);//当前位乘2的-（i-1）次幂也就是除2的i-1次幂  
    }
    // 合并结果
    double decimal_value = int_value + frac_value;  
    sprintf(result, "%lf", decimal_value);
}
int main() {
    char input[100],numstr[100],n1[100],result[100]; 
    PointFixedNum num;
    scanf("%s",input);
    if(numstr[0]=='-') {
        num.sign=-1;
        printf("-");
    }
    else num.sign=1;
    if (is_decimal(input)) {
        // 提取数字部分
        strncpy(numstr, input, strlen(input) - 1);
        strcpy(n1, num.sign==-1 ? numstr + 1 : numstr);//如果第一位是“-”就将num指针指向下一位
        //n1[strlen(input) - 1] = '\0';
        init(&num,input);
        printf("%s.%sD\n",num.integer,num.integer);
    } else if (is_binary(input)) {
        // 提取数字部分
        strncpy(numstr, input, strlen(input) - 1);
        strcpy(n1, num.sign==-1 ? numstr + 1 : numstr);//如果第一位是“-”就将num指针指向下一位
        n1[strlen(input) - 1] = '\0';
        // 转换为十进制
        BtoD(numstr, result);
        init(&num,result);
        printf("%s.%sD\n",num.integer,num.integer);
    }
    return 0;  
}
```
## 任务3