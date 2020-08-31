PAT刷题

#### 1012

```c++
#include <iostream>
#include <vector>
using namespace std;
int main() {
    int n, num, A1 = 0, A2 = 0, A5 = 0;
    double A4 = 0.0;
    cin >> n;
    vector<int> v[5];   // 建立一个一维数组，数组中每个元素是一个vector
    for (int i = 0; i < n; i++) {
        cin >> num;
        v[num%5].push_back(num);
    }
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < v[i].size(); j++) {
            if (i == 0 && v[i][j] % 2 == 0) A1 += v[i][j];		// 坑
            if (i == 1 && j % 2 == 0) A2 += v[i][j];
            if (i == 1 && j % 2 == 1) A2 -= v[i][j];
            if (i == 3) A4 += v[i][j];
            if (i == 4 && v[i][j] > A5) A5 = v[i][j];
        }
    }
    for (int i = 0; i < 5; i++) {
        if (i != 0) printf(" ");
        if (i == 0 && A1 == 0 || i != 0 && v[i].size() == 0) {		// 坑
            printf("N"); continue;
        }
        if (i == 0) printf("%d", A1);
        if (i == 1) printf("%d", A2);
        if (i == 2) printf("%d", v[2].size());
        if (i == 3) printf("%.1f", A4 / v[3].size());	// 巨坑
        if (i == 4) printf("%d", A5);
    }
    return 0;
}

// 思路：建立一个一维数组，数组中每个元素是一个vector容器，用于分类 输入的数字除以5之后的各种情况

/*
 * 坑点：
 *  1. 审题A1那里还有一个条件是偶数，当输入的只有奇数能被5除尽，那么容器size就不为0了
 *  2. 整数类型(int)的数，用printf（“%f”）打印出来的结果为0.000000   # 这个巨坑！！！
 */
```



#### 1013

```c++
#include <iostream>
#include <vector>
using namespace std;

bool is_prime(int a) {
    for(int i = 2; i * i <= a; i++)  	// 坑，这里是<=(假如是<的话，2, 4都没进来就判断为素数了)
        if(a % i == 0) return false;
    return true;
}


int main() {

    int m, n, cnt = 0, num = 2;
    scanf("%d %d", &m, &n);

    vector<int> v;
    while (cnt < n) {
        if(is_prime(num)) {
            cnt++;
            if(cnt >= m) v.push_back(num);
        }
        num++;
    }

    cnt = 0;        // 控制空格和换行，这里的骚套路得记住
    for(int i = 0; i < v.size(); i++) {
        cnt++;
        if(cnt % 10 != 1) printf(" ");
        printf("%d", v[i]);
        if(cnt % 10 == 0) printf("\n");
    }
}
// 思路：vector中保存第M到第N个素数，用cnt标记输出了多少个，如果当前已经输出的个数为10的倍数，则输出一个空行～

// 坑：输出格式控制：'数字 数字 数字'，一般我们要用' 数字'去接前面那个数字，这样到最后就不会差生多一个空格这样的情况了
```





#### 1014

```c++
#include <iostream>
#include <cctype>
using namespace std;
int main() {
    string a, b, c, d;
    cin >> a >> b >> c >> d;
    char t[2];
    int pos, i = 0, j = 0;
    while(i < a.length() && i < b.length()) {
        if (a[i] == b[i] && (a[i] >= 'A' && a[i] <= 'G')) {			// 坑，记住是'<='
            t[0] = a[i];
            break;			// 坑
        }
        i++;
    }
    i = i + 1;
    while (i < a.length() && i < b.length()) {
        if (a[i] == b[i] && ((a[i] >= 'A' && a[i] <= 'N') || isdigit(a[i]))) {
            t[1] = a[i];
            break;			// 坑
        }
        i++;
    }
    while (j < c.length() && j < d.length()) {
        if (c[j] == d[j] && isalpha(c[j])) {
            pos = j;
            break;			// 坑
        }
        j++;
    }
    string week[7] = {"MON", "TUE", "WED", "THU", "FRI", "SAT", "SUN"};
    int m = isdigit(t[1]) ? t[1] - '0' : t[1] - 'A' + 10;
    cout << week[t[0]-'A'];
    printf(" %02d:%02d", m, pos);
    return 0;
}

// 思路：就是遍历+字符串处理
// 坑：判断大写字母(target <= 'A' && target >= 'Z')，记住是'<='而不是'<'
// 坑：遍历到第一个字符相等的时候就要退出循环，否则会遍历到最后一个两字符相等的位置
```



#### 1015

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct node {
    int id, de, cai;
};

bool cmp(struct node a, struct node b) {
    if((a.de + a.cai) != (b.de + b.cai))
        return (a.de + a.cai) > (b.de + b.cai);
    else if(a.de != b.de)
        return a.de > b.de;
    else
        return a.id < b.id;
}

int main() {

    int n, low, high;
    cin >> n >> low >> high;
    vector<node> v(4);      // 表示4类考生
    node temp;
    int total = n;
    for(int i = 0; i < n; i++) {
        scanf("%d %d %d", &temp.id, &temp.de, &temp.cai);
        // 初筛
        if(temp.de < low || temp.cai < low)
            total--;
        else if(temp.de >= high && temp.cai >= high) v[0].push_back(temp);
        else if(temp.de >= high) v[1].push_back(temp);
        else if(temp.de >= temp.cai) v[2].push_back(temp);		// 坑
        else v[3].push_back(temp);
    }

    cout << total << endl;
    for(int i = 0; i < 4; i++) {
        sort(v[i].begin(), v[i].end(), cmp);
        for(int j = 0; j < v[i].size(); j++) {
            printf("%d %d %d\n", v[i][j].id, v[i][j].de, v[i][j].cai);
        }
    }
    return 0;
}

// 思路：定义结构体存放学生信息，定义一维数组，每一维存放一个vector，进行分类学生，之后每一类单独排序然后输出即可. cmp函数一定要按照题目要求写，在输出描述那里有说明。

// 坑点：不低于也就是大于等于-->延伸：不高于就是小于等于
```





#### 1016

```c++
#include <iostream>
using namespace std;
int main() {
    string a, b;
    int da, db, pa = 0, pb = 0;
    cin >> a >> da >> b >> db;
    for (int i = 0; i < a.length(); i++)
        if (da == (a[i] - '0')) pa = pa * 10 + da;
    for (int i = 0; i < b.length(); i++)
        if (db == (b[i] - '0')) pb = pb * 10 + db;
    cout << pa + pb;
    return 0;
}

// 思路：用字符串存比较好找每一位数字
// 坑点：全程无坑点，比较容易拿分
```





#### 1017

```c++
#include <iostream>
#include "vector"

using namespace std;

int main() {
    string s;
    vector<int> v;	// 用来存放商数
    int a, t, temp = 0;
    cin >> s >> a;
    int len = s.length();
    for(int i = 0; i < len; i++) {
        t = (temp * 10 + (s[i] - '0')) / a;
        v.push_back(t);
        temp = (temp * 10 + (s[i] - '0')) % a;
    }
    // 去除前导 0，并输出结果
    bool flag = false;
    for(int i = 0; i < v.size(); i++) {
        if(v[i] != 0) flag = true;
        if(flag)
            cout << v[i];
    }
    cout << " " << temp;
    return 0;
}
// 思路：手动模拟除法运算：从第一位开始除，余数*10连上下一位再除，依此反复直到得出结果
// 坑点：要去掉前导的0 (即商数第一位不为0)
```





#### 1018

```c++
#include <iostream>
#include "map"
using namespace std;

int main() {
    int n;
    cin >> n;
    char a[n], b[n];
    map<char, int> ma, mb;
    int win_a = 0, win_b = 0, ping = 0;
    for(int i = 0; i < n; i++) {
        scanf("%s %s", &a[i], &b[i]);
        if(a[i] == b[i]) ping++;
        else if((a[i] == 'B' && b[i] == 'C') || (a[i] == 'C' && b[i] == 'J') || (a[i] == 'J' && b[i] == 'B')) {
            ma[a[i]] += 1;
            win_a++;
        }
        else {
            mb[b[i]] += 1;
            win_b++;
        }
    }

    char max_a = 'B', max_b = 'B';
    for(map<char, int>::iterator it = ma.begin(); it != ma.end(); it++) {
        max_a = it->second > ma[max_a] ? it->first : max_a;
    }
    for(map<char, int>::iterator it = mb.begin(); it != mb.end(); it++) {
        max_b = it->second > mb[max_b] ? it->first : max_b;
    }

    printf("%d %d %d\n", win_a, ping, win_b);
    printf("%d %d %d\n", win_b, ping, win_a);
    printf("%c %c", max_a, max_b);				// 坑

    return 0;
}

// 思路：用map<char, int>来存储每个出拳赢的次数，举例：mp['C'] = 3; 就代表出锤子赢了3次
// 坑点：输出格式，格式控制符要细心
```





#### 1019

```c++
#include <iostream>
#include <algorithm>
using namespace std;

bool cmp(char a, char b) {return a > b;}	// cmp

int main() {
    string s;
    cin >> s;
    s.insert(0, 4 - s.length(), '0');	// 用来给不足4位的时候前面补0～
    do {
        string a = s, b = s;
        sort(a.begin(), a.end(), cmp);
        sort(b.begin(), b.end());
        int result = stoi(a) - stoi(b);		// stoi() 将string转成int
        s = to_string(result);
        s.insert(0, 4 - s.length(), '0');
        cout << a << " - " << b << " = " << s << endl;
    } while (s != "6174" && s != "0000");
    return 0;
}

// 思路：巧妙之处就在于用string来接收并操作，然后将其转换成int来运算，再转成string来操作
// 坑点：用do..while就是当输入数字就是6174的时候还能进行一系列的运算
// 新知识：
	1. s.insert(0, 4 - s.length(), '0');	// 用来给不足4位的时候前面补0～
	2. stoi() 可以将string转成int
```





#### 1020

```c++
#include <iostream>
#include <algorithm>
using namespace std;

struct node {
    double num, total_price, price;     // 数量，总价，单价
}cake[100010];

bool cmp(struct node a, struct node b) {
    return a.price > b.price;
}

int main() {
    int n;
    double need;        // 需求
    cin >> n >> need;

    for(int i = 0; i < n; i++) {
        cin >> cake[i].num;
    }
    for(int i = 0; i < n; i++) {
        cin >> cake[i].total_price;
        cake[i].price = cake[i].total_price / cake[i].num;
    }

    sort(cake, cake + n, cmp);

    int index = 0;
    double res = 0.0;
    while (need > 0) {
        if(cake[index].num <= need)
            res = res + cake[index].total_price;    // 总量 <= 需求
        else
            res = res + need * cake[index].price;   // 总量 > 需求
        need -= cake[index].num;
        index++;        	// 坑
    }

    printf("%.2f", res);		// 坑
    return 0;
}
// 思路：定义一个结构体存放月饼的：数量，总价，单价，当(总量 <= 需求)时，直接加该种类的总价即可，当(总量>需求)时，加需求*单价
// 坑点：
	1. 全程运算要用double型
    2. 索引记得变化，while循环中总是忘记操作索引
    3. 注意输出格式
        
```



#### 1021

```c++
#include <iostream>
using namespace std;

int main() {
    string s;
    cin >> s;
    int arr[10] = {0};
    for(int i = 0; i < s.length(); i++) {
        arr[s[i] - '0']++;
    }
    for(int i = 0; i < 10; i++) {
        if(arr[i] != 0)
            printf("%d:%d\n", i, arr[i]);
    }
    return 0;
}
// 思路：arr[目标数字] = 出现次数
// 坑点：暂无
```





#### 1022

```c++
#include <iostream>
#include "vector"
using namespace std;

int main() {
    long long a, b, c;
    int d;
    cin >> a >> b >> d;
    c = a + b;
    vector<int> v;
    while (c > 0) {
        v.push_back(c % d);
        a = c / d;		// 使用a做中间变量节省重新定义变量的空间，其实用temp代码会更清晰
        c = a;
    }
    for (int i = v.size() - 1; i >= 0; i--) {		// 倒叙输出vector	// 坑
        cout << v[i];
    }
}
// 思路：将每一次c % d的结果保存在int类型的vector中，然后将c / d，直到 c 等于 0为止，此时vector中保存的就是 c 在 D 进制下每一位的结果的倒序，最后倒序输出即可
// 坑点：倒叙输出的时候注意索引变化：i--
```



#### 1023

```c++
#include <iostream>
#include <algorithm>
using namespace std;

int main() {
    int arr[10] = {0};
    for(int i = 0; i < 10; i++) {
        cin >> arr[i];
    }
    for(int i = 1; i < 10; i++) {       // 输出第一位数(第一位需要非0)
        if(arr[i] > 0) {
            cout << i;
            arr[i]--;
            break;
        }
    }
    for(int i = 0; i < 10; i++) {		// 后面按顺序输出即可
        while(arr[i] > 0) {
            cout << i;
            arr[i]--;
        }
    }
    return 0;
}
// 思路：arr[数字] = 出现次数
// 坑点：审题要细心
```



#### 1024

```c++
// 写在前面：科学计数法就是把一个数表示为 x.xx * 10^n
// 比如：123400 就是 1.234 x 10^5 科学计数法表示：+1.234E+05
// 又比如： -0.04321 就是 4.321 x 10^-2 科学计数法表示：-4.321E-02

#include <iostream>
using namespace std;
int main() {
    string s;
    cin >> s;
    int idx = 0;
    while (s[idx] != 'E') idx++;
    string t = s.substr(1, idx-1);    // substr(pos, len);  // 第二个参数时长度，而不是末尾
    int n = stoi(s.substr(idx+1));
    if(s[0] == '-') cout << '-';
    if(n < 0) {     
        cout << "0.";		// 坑
        for(int i = 0; i < abs(n) - 1; i++) cout << '0';		// 坑
        for(int i = 0; i < t.length(); i++)
            if(t[i] != '.')
                cout << t[i];
    } else {
        cout << t[0];		
        int j, cnt;		
        for(j = 2, cnt = 0; j < t.length() && cnt < n; j++, cnt++) cout << t[j];	// 坑
        if(j == t.length()) {   // 表示t已完全输出,后面需补(n-cnt)个0
            for(int k = 0; k < n - cnt; k++)
                cout << '0';
        } else {        	// 表示cnt已完全输出,需要把t剩余的输出
            cout << '.';
            for(int k = j; k < t.length(); k++)
                cout << t[k];
        }
    }
    return 0;
}
// 思路：把E前面的存放在t变量中(仅包含数字，不包括符号)， E后面的存放在n变量中(包含符号，用于判断正负);先判断数字主体部分的正负，然后判断10^n中的n的正负，根据n的大小对主体进行位移
// 坑点：
	1. 输出"0."的时候要用双引号，不能用单引号，他不是一个字符
    2. substr(pos, len);  第二个参数时长度，而不是末尾，这里巨坑
    3. 当n>0的时候，一定要去掉"."再输出t中的数字，当n<0的时候如果t中的数字还没完全输出需要补".",巨坑
    4. 注意当n<0时，需要abs()一下才能作为索引边界
    5. 注意索引，不能大意
```



#### 1025

```c++
#include <iostream>
#include <algorithm>
using namespace std;
int main() {
    int first, k, n, temp;
    cin >> first >> n >> k;
    int data[100005], next[100005], list[100005];
    for (int i = 0; i < n; i++) {
        cin >> temp;
        cin >> data[temp] >> next[temp];
    }
    int sum = 0;	//不一定所有的输入的结点都是有用的，加个计数器
    while (first != -1) {
        list[sum++] = first;
        first = next[first];
    }
    for (int i = 0; i < (sum - sum % k); i += k)		// 坑
        reverse(begin(list) + i, begin(list) + i + k);
    for (int i = 0; i < sum - 1; i++)
        printf("%05d %d %05d\n", list[i], data[list[i]], list[i + 1]);	// 坑
    printf("%05d %d -1", list[sum - 1], data[list[sum - 1]]);		// 坑
    return 0;
}
// 思路：以结点的地址为数组下标，分别定义data[], next[] ; list[]数组是用来存放按顺序连接好的有效结点(因为输入结点是无序的，即不是头尾相连的) ; 然后就让每k个结点进行反转，最后输出即可

// 坑点：
	1. 审题：题目说是每k个结点反转，是'每'
    2. 输出的时候因为结点顺序已经发生改变了，所以next[list[i]]这样的输出是错误的，应该直接输出下一个结点的头结点list[i+1]，也就是当前结点的尾结点了。
    3. 当最后一个输出的时候，因为已经是最后一个了，所以要手动补 -1
```



#### 1026

```c++
#include <iostream>
using namespace std;
int main() {
    int a, b;
    cin >> a >> b;
    int n = ((b - a) + 50) / 100;
    int hour = n / 3600;
    n = n % 3600;
    int minute = n / 60, second = n % 60;
    printf("%02d:%02d:%02d", hour, minute, second);
    return 0;
}
// 思路：关键是要读懂题目，简单来说就是要计算两个数字之间的打点数然后转变为时分秒输出
// 坑点：四舍五入那里，是要和题目所给的打点数单位进行运算的
// 新知识：四舍五入：(n + 0.5) / 1，也就是加上后面除数的一半在进行运算就能达到四舍五入的效果
```





#### 1027

```c++
#include <iostream>
using namespace std;
int main() {
    int N, row = 0;		// row表示向外扩展几行
    char c;
    cin >> N >> c;
    for (int i = 0; i < N; i++) {
        if ((2 * i * (i + 2) + 1) > N) {
            row = i - 1;		// 说明无法扩展到i层，所以要(i-1)
            break;
        }
    }
    for (int i = row; i >= 1; i--) {
        for (int k = row - i; k >= 1; k--) cout << " ";
        for (int j = i * 2 + 1; j >= 1; j--) cout << c;
        cout << endl;
    }
    for (int i = 0; i < row; i++) cout << " ";
    cout << c << endl;
    
    for (int i = 1; i <= row; i++) {
        for (int k = row - i; k >= 1; k--) cout << " ";
        for (int j = i * 2 + 1; j >= 1; j--) cout << c;
        cout << endl;
    }
    cout << (N - (2 * row * (row + 2) + 1));
    return 0;
}

// 思路：每个沙漏都是从最中间一行行向上下分别扩展一行，每次扩展行都要比之前一层多2个符号，最中间一行只有 1 个符号，假设扩展的层数为 i，则扩展出去的上边需要的所有符号个数为3 + 5 + 7 + … + (2i+1) = (3 + 2i + 1) * i / 2 = i * (i + 2)，扩展出去的下边与上边同样多所以乘以2，加上最重要那一行1个符号，所以 总共需要2 * i * (i + 2) + 1个符号，所以i从0到N，找满足(2 * i * (i + 2) + 1) > N的最小的 i，因为符号不能超过N，所以只能扩展出去 i-1 行，用变量row表示从最中间一行需要扩展出去的行数，row = i – 1，接下来开始输出，上面的每一行，对于扩展出去的第 i 层需要输出row – i个空格，接着输出i * 2 + 1个符号c和换行符；对于最中间一行，需要输出row – 1个空格、符号c和换行符；对于下面的每一行，对于扩展出去的第i层，需要输出row-i个空格，接着输出i * 2 + 1个符号c和换行符，因为用掉的符号数为2 * row * (row + 2) + 1，所以最后输出剩下没用掉的符号数为N – (2 * row * (row + 2) + 1)～

// 坑点：全程坑点，好好理解消化
```





#### 1028

```c++
#include <iostream>
using namespace std;
int main() {
    int n, cnt = 0;
    cin >> n;
    string name, birth, old_man, young_man, old_age="2014/09/06", young_age="1814/09/06";
    for(int i = 0; i < n; i++) {
        cin >> name >> birth;
        if(birth >= "1814/09/06" && birth <= "2014/09/06") {	// 新知识
            cnt++;
            if(birth <= old_age) {
                old_age = birth;
                old_man = name;
            }
            if(birth >= young_age) {
                young_age = birth;
                young_man = name;
            }
        }
    }

    cout << cnt << " " << old_man << " " << young_man;		// 坑
    
	// cout << cnt;
    //if (cnt != 0) cout << " " << minname << " " << maxname;
    return 0;
}

// 思路：用字符串接收name和birth，如果当前birth >= "1814/09/06"且<= "2014/09/06"，则是有效生日，有效个数cnt++，如果birth >= maxbirth，则更新maxname和maxbirth的值；如果birth <= minbirth，则更新minname和minbirth的值，这里的max和min是指数值上的大小～最后输出cnt，minname和maxname，minname表示最年长的（生日的数值大小最小的），maxname表示最年轻的（生日的数值大小最大的）～

// 坑点：
	1. 原来这样子的字符串也是可以拿来比较的：(birth >= "1814/09/06"),按照字典序比较
    2. 不能用map<string, string>这样子来先存储，然后比较输出。------> 因为会同名，一个key只能对应一个value，无法解决同名问题，所以最简单就是现在这种解法了。 巨坑
    3. 最后输出如果题目没有说n>0的话要先判断在输出，但是题目说了，就可以一行输出

// 新知识：
	原来这样子的字符串也是可以拿来比较的：(birth >= "1814/09/06"),按照字典序比较
    这样子就方便了很多，不用转成int在进行比较了
```



#### 1029

```c++
#include <iostream>
#include <cctype>
using namespace std;
int main() {
    string s1, s2, ans;
    cin >> s1 >> s2;
    for (int i = 0; i < s1.length(); i++)
        if (s2.find(s1[i]) == string::npos && ans.find(toupper(s1[i])) == string::npos)
            // 在子串中找不到并且在答案串中也找不到，才加入答案串中
            ans += toupper(s1[i]);
    cout << ans;
    return 0;
}
// 思路：遍历父串，在子串中使用find()函数找不到父串的某个字符 并且此字符还没加入过答案串中 就将其加入答案串。
// 坑点：
	1. find(s)查找失败时时返回-1(即string::npos)，而不是0,索引用(!find(s))这种写法时错误的，c++中是非0即true,但是你这个返回的是-1呀大哥！！！巨坑
    2. 注意答案要转成大写并去重
```





#### 1030

```c++
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
int main() {
    int n,p;
    cin >> n >> p;
    vector<int> v(n);		// 坑
    for (int i = 0; i < n; i++)
        cin >> v[i];
    
    sort(v.begin(), v.end());
    int result = 0, temp = 0;	// result代表找到的最长区间长度，temp代表区间长度
    for (int i = 0; i < n; i++) {
        for (int j = i + result; j < n; j++) {
            if (v[j] <= v[i] * p) {
                temp = j - i + 1;		// 坑
                if (temp > result)
                    result = temp;
            } else {
                break;
            }
        }
    }
    cout << result;
    return 0;
}


// 思路：首先将数列从小到大排序，设当前结果为result = 0，当前最长长度为temp = 0；从i = 0~n，j从i + result到n，【因为是为了找最大的result，所以下一次j只要从i的result个后面开始找就行了】每次计算temp若大于result则更新result，最后输出result的值～

// 坑点：
	1. 要用v[i]作为输入参数接收，就必须在定义的时候vector<int> v(n);加上个(n)，不然不会报错但是就是让你抓狂！~！！！
    2. 因为索引是从0开始的，所以找[i, j]的区间长度就是(j - i + 1)
```





#### 1031

```c++
#include <iostream>
using namespace std;
int quan[] = {7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2};
int M[] = {1, 0, 10, 9, 8, 7, 6, 5, 4, 3, 2};

bool isTrue(string s) {
    int sum = 0;
    for (int i = 0; i < s.length() - 1; i++) {
        if (s[i] < '0' || s[i] > '9') return false;
        sum += (s[i] - '0') * quan[i];
    }
    int temp = (s[17] == 'X') ? 10 : (s[17] - '0');     // 身份证最后一位
    int z = sum % 11;         // z值
    return M[z] == temp;		// 坑
}
int main() {
    int n, flag = 0;
    cin >> n;
    string s;
    for (int i = 0; i < n; i++) {
        cin >> s;
        if (!isTrue(s)) {
            cout << s << endl;
            flag = 1;
        }
    }
    if (flag == 0) cout << "All passed";
    return 0;
}

// 读懂题目：就是首先对前17位数字加权求和(sum)，然后取模(sum % 11)得到一个数字z，然后z根据对应关系得到校验码M，如果身份证最后一位与校验码M相同，则此身份证有效

// 思路：isTrue函数判断身份证号是否正常，如果不正常返回false，判断每一个给出的身份证号，如果不正常，就输出这个身份证号，并且置flag=1表示有不正常的号码，如果所有的号码都是正常，即flag依旧等于0，则输出All passed～在isTrue函数中，先判断前17位是否是数字，如果不是，直接return false，如果是，就将当前字符转化为数字并与a[i]相乘，累加在sum中，对于第18位，如果是X要转化为10～比较b[sum%11]和第18位是否相等，如果相等就返回true，不相等就返回false～

// 坑点：
	1. 读懂题目
	2. isTrue()函数中，最后返回值那里：return M[z] == temp;		// 双等号才是判断
		// 写成等号它不会报错，正常运行，但是结果就是不对.......巨坑！！！！！
```



#### 1032

```c++
#include <iostream>
#include <vector>
using namespace std;
int main() {
    int N;
    cin >> N;
    vector<int> a(N + 1);		// 因为学校编号是从1开始，所以这里有可能有学校n，因此要+1个内存
    int num, score;
    for (int i = 0; i < N; i++) {
        cin >> num >> score;
        a[num] += score;
    }
    int max = a[1], t = 1;
    for (int i = 2; i <= N; i++) {		// 这里要注意题目说的学校编号是从1开始，所以是'<='
        if (max < a[i]) {
            max = a[i];
            t = i;
        }
    }
    cout << t << " " << max;
    return 0;
}

// 思路：用vector来存储比赛信息，v[学校编号] = 学校总分， 遍历比较输出即可。当然用int数组也可.

// 坑点：
	1. 学校编号是从1开始的，vector要多给一个内存空间
    2. 遍历的时候也要注意下标

```





#### 1033

```c++
#include <iostream>

using namespace std;
int main() {
    string huai, s;
    cin >> huai >> s;
    for(int i = 0; i < s.length(); i++) {
        if(huai.find(toupper(s[i])) != string::npos) continue;		// 坑
        if(isupper(s[i]) && huai.find("+") != string::npos) continue;	// 坑
        cout << s[i];
    }
    return 0;
}

// 审题：注意审题，题目说坏键盘都是大写字母，仅仅是'+'号代表上档键
// 坑点：
	1. 注意括号，写太多表达式会乱掉！！！
    2. 最好用string::npos代替-1，不然总是会忘记找不到时的返回值是-1
```



#### 1036

```c++
#include <iostream>

using namespace std;

int main() {

    int n;
    char ch;
    cin >> n >> ch;
    int row = (n + 1) / 2;		// 行数要四舍五入，所以要(n + k/2) / k

    for(int i = 0; i < n; i++) {
        cout << ch;
    }
    cout << endl;
    for(int i = 0; i < row - 2; i++) {
        cout << ch;
        for(int j = 0; j < n - 2; j++) {
            cout << " ";
        }
        cout << ch << endl;
    }
    for(int i = 0; i < n; i++) {
        cout << ch;
    }

    return 0;
}

// 思路：直接一行一行输出即可，看清楚题目要求四舍五入
// 坑点：四舍五入：(n + k/2) / k;
```







#### 1037

```c++
#include <iostream>
using namespace std;
int main() {
    int a, b ,c, m, n, t, x, y, z;  // abc应付钱， mnt拥有钱， xyz剩余或欠款
    scanf("%d.%d.%d %d.%d.%d",&a, &b, &c, &m, &n, &t);
    if (a > m || (a == m && b > n) || (a == m && b == n && c > t)) {
        swap(a, m);
        swap(b, n);
        swap(c, t);
        printf("-");
    }
    z = t < c ? t - c + 29 : t - c;
    n = t < c ? n - 1 : n;          // 检查有没有借位
    y = n < b ? n - b + 17 : n - b;
    x = n < b ? m - a - 1 : m - a;      // 这里其实是跳步了，大佬级别的代码
    printf("%d.%d.%d", x, y, z);
    return 0;
}
// 思路：abc代表应付的钱，mnt代表拥有的钱，首先判断应付的钱是否大于拥有的钱，如果大于，说明钱不够，为了简化计算，将a和m、b和n、c和t调换，使得计算(mnt-abc)时是大的减去小的～调换之后别忘记输出负号～xyz分别代表找的钱，从低位向高位计算，如果t < c，说明要向前借位，借一位后自己加上29，否则z = t - c即可～别忘记如果有借位，n要减去1～然后计算中间位，如果n < b，说明要借位，则y = n - b + 17，否则不用借位，y = n - b即可～最后计算最高位x，如果n < b引起了借位，则x = m - a - 1，否则x = m - a即可～最后输出x.y.z～

// 坑点：自己思路不够大佬的好，无论用字符串还是用数组 都比不上直接算来的简单快捷
```





#### 1038

```c++
#include <iostream>
#include "vector"
using namespace std;
int main() {

    int n, k, temp;
    vector<int> v(101);     // v[分数] = 人数
    cin >> n;
    for(int i = 0; i < n; i++) {
        cin >> temp;
        v[temp]++;
    }
    cin >> k;
    for(int i = 0; i < k; i++) {
        cin >> temp;
        if(i != 0)
            cout << " ";
        cout << v[temp];    // vector默认初始化为0，所以不用判断v[temp]直接输出即可
    }
    return 0;
}
// 思路：常用套路了：v[分数] = 人数
// 坑点：vector默认初始化为0，直接输出即可，不用谢太多多余的代码
```





#### 1039

```c++
#include <iostream>

using namespace std;
int main() {
    string s, need;
    cin >> s >> need;

    int book[100010];       // 管他那么多，就是要开那么大
    for(int i = 0; i < s.length(); i++) {
        book[s[i]]++;
    }
    int que = 0;        
    for(int i = 0; i < need.length(); i++) {
        if(book[need[i]] > 0)
            book[need[i]]--;
        else
            que++;
    }
    if(que > 0)
        cout << "No" << " " << que;
    else
        cout << "Yes" << " " << s.length() - need.length();

    return 0;
}

// 思路：字符串a和b分别存储摊主的珠串和小红想做的珠串，遍历字符串a，将每一个字符出现的次数保存在book数组中，表示摊主的每个珠子的个数，遍历字符串b，如果book[b[i]]>0，表示小红要的珠子摊主有，则book[b[i]]-1，将这个珠子给小红～否则说明小红要的珠子摊主没有，则将统计缺了多少珠子的result++，如果result不等于0，说明缺珠子，则不可以买，输出No以及缺了的珠子个数result，否则说明不缺珠子，可以买，输出Yes以及摊主珠子多余的个数a.length() – b.length()～

// 坑点：
	1. 没必要用map，因为都可以用整型表示
    2. 尽管开100010，不要算得太死开得太少内存，不然会过不了
    3. 是先存店主有的，然后小红需要就减减，而不是相反.......这里很坑，才导致我用map搞得很复杂的亚子
```



#### 1040

```c++
#include <iostream>

using namespace std;
int main() {
    string s;
    cin >> s;
    int len = s.length(), countp = 0, countt = 0, res = 0;
    for(int i = 0; i < len; i++) {
        if(s[i] == 'T')
            countt++;
    }
    for(int i = 0; i < len; i++) {
        if(s[i]=='P') countp++;
        if(s[i]=='T') countt--;
        if(s[i] == 'A') res = (res + (countp * countt) % 1000000007) % 1000000007;
    }
    cout << res;
    return 0;
}
// 思路：要想知道构成多少个PAT，那么遍历字符串后对于每一A，它前面的P的个数和它后面的T的个数的乘积就是能构成的PAT的个数。然后把对于每一个A的结果相加即可~~辣么就简单啦，只需要先遍历字符串数一数有多少个T~~然后每遇到一个T呢~countt–;每遇到一个P呢，countp++;然后一遇到字母A呢就countt * countp~~~把这个结果累加到result中~~~~最后输出结果就好啦~~对了别忘记要对10000000007取余哦~~~~

// 坑点：我不会，没思路.......

// 奇怪的知识：
为什么要对1000000007取模（取余）
大数阶乘，大数的排列组合等，一般都要求将输出结果对1000000007取模（取余）。为什么总是1000000007呢？
	1. 1000000007是一个质数（素数），对质数取余能最大程度避免冲突～
	2. int32位的最大值为2147483647，所以对于int32位来说1000000007足够大
	3. int64位的最大值为2^63-1，对于1000000007来说它的平方不会在int64中溢出，所以在大数相乘的时候，因为(a∗b)%c=((a%c)∗(b%c))%c，所以相乘时两边都对1000000007取模，再保存在int64里面不会溢出
```









































































































































































































































































































