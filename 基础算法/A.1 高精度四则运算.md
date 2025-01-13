# 高精度加法

### 1. 用`字符串`读入高精度数字
   
### 2. 把字符串`翻转`存入两个整型数组
   
### 3. 从低位到高位，`累加`、`进位`、`存余`

### 4. 把答案数组`高位到低位`依次输出

### 代码：

```cpp
vector<int> add(vector<int> A, vector<int> B)
{
    //保证 A 的位数始终大于 B
    if(A.size() < B.size()) return add(B, A);

    //声明一个 t 用于维护进位
    int t = 0;
    //声明一个数组 C 用于存储答案
    vector<int> C;

    //模拟竖式运算
    for(int i = 0; i < A.size(); i ++ )
    {
        //当 i 小于 B 的位数时，让 t = A[i] + B[i]
        //当 i 大于 B 的位数时，t = A[i]
        t += A[i];
        if(i < B.size()) t += B[i];
        //将 t 的个位存入答案数组中，十位作为进位保留到下一位的运算
        C.push_back(t % 10);
        t /= 10;
    }
    
    //处理最后一个进位
    if(t) C.push_back(1);

    return C;
}

int main()
{
    //存储相加的 A 和 B
    string a, b;
    cin >> a >> b;
    vector<int> A, B;
    for(int i = a.size() - 1; i >= 0; i -- ) A.push_back(a[i] - '0');
    for(int i = b.size() - 1; i >= 0; i -- ) B.push_back(b[i] - '0');

    //调用高精度加法函数
    auto C = add(A, B);

    //从高位到低位输出答案
    for(int i = C.size() - 1; i >= 0; i -- ) cout << C[i];

    return 0;
}
```
#
# 高精度减法

### 1. 用`字符串`读入高精度数字
   
### 2. 把字符串`翻转`存入两个整型数组

### 3. 若`A < B`，则交换 A, B，输出负号

### 4. 从低位到高位，`逐位求差`、`借位`、`存差`

### 5. 把数组 C 从`高位到低位`依次输出

### 代码：

```cpp
//判断 A，B 的大小关系
bool cmp(vector<int> A, vector<int> B)
{
    //通过数字位数判断大小
    if(A.size() != B.size()) return A.size() > B.size();
    //由高位到低位判断每一位的大小关系
    for(int i = A.size() - 1; i >= 0; i -- )
    {
        if(A[i] != B[i]) return A[i] > B[i];
    }
    // A，B 大小相等
    return true;
}

vector<int> sub(vector<int> A, vector<int> B)
{
    // t 维护借位，C 维护答案
    int t = 0;
    vector<int> C;

    for(int i = 0; i < A.size(); i ++ )
    {
        //先处理借位
        t = A[i] - t;
        //减去 B 相应位置上的数字
        if(i < B.size()) t -= B[i];
        //确保存入 C 中的数字为正数
        C.push_back((t + 10) % 10);
        //存储借位
        if(t < 0>) t = 1;
        else t = 0;
    }

    //去前导零
    while(C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}

int main()
{
    //存储高精度数字
    string a, b;
    cin >> a >> b;
    vector<int> A, B;
    for(int i = a.size() - 1; i >= 0; i -- ) A.push_back(a[i] - '0');
    for(int i = b.size() - 1; i >= 0; i -- ) B.push_back(b[i] - '0');

    //判断 A 和 B 的大小决定答案的符号
    if(!cmp(A, B)) swap(A, B), cout << '-';

    //调用高精度减法函数
    auto C = sub(A, B);

    //输出答案
    for(int i = C.size() - 1; i >= 0; i -- ) cout << C[i];

    return 0;
}
```
#
# 高精度乘法（一）：高精度×低精度

### 1. 用`字符串`存储高精度数字

### 2. 把字符串`翻转`存入整型数组

### 3. 从低位到高位依次`相乘`、`进位`、`存余`

### 4. 从高位到低位输出答案

### 代码：

```cpp
vector<int> mul(vector<int> A, int B)
{
    // t 维护进位，C 维护答案
    int t = 0;
    vector<int> C;

    for(int i = 0; i < A.size(); i ++ )
    {
        //处理进位以及每位相乘的结果
        t += A[i] * B;
        //取个位数字存入答案数组中，其余位置当作进位处理
        C.push_back(t % 10);
        t /= 10;
    }

    //处理最后一次相乘的进位
    while(t)
    {
        C.push_back(t % 10);
        t /= 10;
    }

    //去前导零
    while(C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}

int main()
{
    //存储高精度数字
    string a;
    int B;
    cin >> a >> B;
    vector<int> A;
    for(int i = a.size() - 1; i >= 0; i -- ) A.push_back(a[i] - '0');
    
    //调用高精度乘法函数
    auto C = mul(A, B);

    //由高位到低位输出答案
    for(int i = C.size() - 1; i >= 0; i -- ) cout << C[i];

    return 0;
}

```
#
# 高精度乘法（二）：高精度×高精度

### 1. 高精度数字利用`字符串`读入

### 2. 把字符串`翻转`存入两个整型数组 A, B

### 3. 从低位到高位，`累加乘积`、`进位`、`存余`

### 4. 从高位到低位输出答案

### 代码：

```cpp
vector<int> mul(vector<int> A, vector<int> B)
{
    // C 维护答案，注意这里要预分配内存
    vector<int> C(A.size() + B.size());
    //对每一项相乘得到的 Cn 进行累加，暂不处理进位
    for(int i = 0; i < A.size(); i ++ )
        for(int j = 0; j < B.size(); j ++ )
            C[i + j] += A[i] * B[j];

    // t 维护进位
    int t = 0;
    //处理每一位数字的进位
    for(int i = 0; i < C.size(); i ++ )
    {
        t += C[i];
        C[i] = t % 10;
        t /= 10;
    }

    //去前导零
    while(C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}
 
int main()
{
    //存储高精度数字
    string a, b;
    cin >> a >> b;
    vector<int> A, B;
    for(int i = a.size() - 1; i >= 0; i -- ) A.push_back(a[i] - '0');
    for(int i = b.size() - 1; i >= 0; i -- ) B.push_back(b[i] - '0');

    //调用高精度乘法函数
    auto C = mul(A, B);

    //从高位到低位输出答案
    for(int i = C.size() - 1; i >= 0; i -- ) cout << C[i];

    return 0;
}
```
#
# 高精度除法

### 1. 高精度数字利用`字符串`读入

### 2. 把字符串`翻转`读入整型数组

### 3. 从`高位到低位`，`当前被除数`、`存商`、`余数`

### 4. 把答案从高位到低位依次输出

### 代码：

```cpp
vector<int> div(vector<int> A, int B, long long &r)
{
    // C 维护答案，r 维护余数
    vector<int> C;
    r = 0;

    for(int i = A.size() - 1; i >= 0; i -- )
    {
        r = r * 10 + A[i]; //当前被除数
        C.push_back(r / B); //商
        r %= B; //余数
    }
    
    //翻转数组 C
    reverse(C.begin(), C.end());

    //去前导零
    while(C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}

int main()
{
    //存储高精度数字
    string a;
    int B;
    cin >> a >> B;
    vector<int> A;
    for(int i = a.size() - 1; i >= 0; i -- ) A.push_back(a[i] - '0');

    // r 维护余数，调用高精度除法函数
    long long r;
    auto C = div(A, B, r);

    // 从高位到低位输出答案，输出余数
    for(int i = C.size() - 1; i >= 0; i -- ) cout << C[i];
    cout << endl << r;

    return 0;
}
```