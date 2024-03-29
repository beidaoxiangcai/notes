1. 快慢指针找环入口：
   设head到环入口的距离为a，快慢指针相遇的点距环入口距离为b，环的长度为L。  
   **注**：快慢指针相遇时，慢指针所走的路程a+b。  
   2*(a+b) = a+b+n*L  
   a = n*L - b  
   因此，fast此时再走a距离，即2a+b+n*L = a + 2n*L，也就是此时fast位于环入口处。  
   所以创建一个指针指向head，和fast一起往后走，他们相遇时即为环入口。

2. priority_queue优先队列：默认为大顶堆  
   小顶堆写法示例：priority_queue<int, vector<int>, greater<int>>。
   - push/pop(): o(log n) 
   - top(): o(1)
   - 第一个非叶结点下标：length / 2 - 1
   - 左孩子下标：2 * i + 1
   - 右孩子下标：2 * i + 2
   - 父亲下标：（i - 1）/ 2   
  将原有数组原地建大顶堆：从第一个非叶结点开始每个节点都做下沉，即与左右孩子比较、交换并递归做交换的结点。  
  取堆顶： 将堆顶与最后一个元素交换，然后下沉堆顶。  
  因此，将下沉操作写成函数如下：
  ``` c++
  void BuildMaxPile(vector<int>& nums, int i){//下沉
      int largest = i, left = i*2+1, right = left + 1;

      if(left < nums.size() && nums[left] > nums[largest]){
         largest = left;
      }

      if(right < nums.size() && nums[right] > nums[largest]){
         largest = right;
      }
      
      if(largest != i){//如果i是最大的，直接返回，否则交换nums[i]和最大的，并继续下沉
         swap(nums[i], nums[largest]);
         BuildMaxPile(nums, largest);
      }
      return;
   }
   ``` 
3. 单调栈：栈内元素或以元素为下标的值单调递增或递减，即入栈操作不破坏栈的单调性。用于解决找最近一个比当前元素大或小的元素。
4. 升序合并两个有序链表的函数：
- new一个结点，用它的next保存合并后的头。
- 用last保存已合并的末尾。
- 若两个链表都存在，就比较大小，并把小的放在last的后面，并更新last。
- 若有一个链表空了，直接把另一个接在last后面。
5. delete操作会释放指针所指向的内存，并调用该内存中对象的析构函数，需要手动将指针置为NULL。
6. 空指针实际上存储了一个特殊的值，表示它不指向任何有效的内存地址。所以不能对空指针进行解引用，因为不存在这样的地址。
7. - 空悬指针：指向已释放的内存地址的指针。
   - 野指针：未初始化的指针，指向一个随机值。
8. 任何一个正整数都可以表示成不超过四个整数的平方之和
9. 可用pair<T1,T2>(x,y)或make_pair(x,y)来创建一个pair。

``` C++
auto pair1 = pair<int, int>(1,2);
auto pair2 = make_pair("abc", 2);
//pair1.first = 1;
//pair2.second = 2; 
```
10.  priority_queue里面放pair，默认用pair.first来比较大小。
11. 字符串匹配——KMP算法：  
    参考：[https://www.zhihu.com/question/21923021](https://www.zhihu.com/question/21923021)  
    ![](kmp.png)
    求next数组：  
    next[x] 定义为： P[0]~P[x] 这一段字符串，使得k-前缀恰等于k-后缀的最大的k.
    - 当p[i + 1] == p[x + 1],next[i + 1] = next[i] + 1;  
    - 当p[i + 1] != p[x + 1].
    > 求使得 A的k-前缀等于B的k-后缀 的最大的k。串A和串B是相同的！B的后缀等于A的后缀！因此，使得A的k-前缀等于B的k-后缀的最大的k，其实就是串A的最长公共前后缀的长度 —— next[now-1]！
     ``` C++
    class Solution {
    public:
        //部分匹配表
        void matchForm(string needle,vector<int>& next){
            int k = 0;//模式串下标
            for(int i = 1; i < needle.size(); i++){//i为next数组下标
                while(k > 0 && needle[k] != needle[i]){
                    k = next[k - 1];
                }
                if(needle[k] == needle[i]){
                    next[i] = k + 1;
                    k++;
                }
                if(k == 0){
                    next[i] = 0;
                }
            }
            return;
        }
        int strStr(string haystack, string needle) {
            vector<int> next(needle.size(), 0);
            matchForm(needle, next);
            int i = 0, j = 0;//i表示haystack正在比较的下标；j是neddle上的
            for(; j < needle.size() && i < haystack.size();){
                if(haystack[i] == needle[j]){
                    j++;
                    i++;
                }else if(j == 0){
                    i++;
                }else{
                    j = next[j - 1];
                }
            }
            if(j == needle.size()){
                return i - j;
            }else{
                return -1;
            }
            
        }
    };
    ```  
12.  前缀树：用于存储、查询字符串、查询字符串前缀。  
    节点：   
    - bool isEnd; //用于指示一个单词结束了  
    - vector<Trie*> next(26, nullptr); //i为当前字母字典序，next[i] != nullptr表示这个字母存在，并指向下一个节点。若当前字母为最后一个字母，下一节点的isEnd = true,且next数组元素全为nullptr。
13. 前缀和：力扣560-求nums连续子数组和为k的个数
    - pre[n]表示nums[0]+...+nums[n]的总和；
    - pre[i] - pre[j - 1] == k, 即表示nums[j]+..+nums[i]和为k；
    - pre[j - 1] = pre[i] - k.
    - 因此，问题转化为求出每个pre[i]时，pre[i]-k 存在的个数即为以i结尾的连续和为k的个数。
    - 可以用unordered_map存储求出的每个pre[i],并记录数值出现的次数。
    ```C++
    class Solution {
    public:
        int subarraySum(vector<int>& nums, int k) {
            //pre[n]表示nums[0]至nums[n]的和，pre[i] - pre[j - 1] == k, 即表示nums[j]至nums[n]和为k.
            unordered_map<int, int> myMap;
            myMap[0] = 1;//使得最小的j从0开始
            int pre = 0, count = 0;
            for(int i = 0; i < nums.size(); i++){
                pre += nums[i];
                if(myMap.find(pre - k) != myMap.end()){
                    count += myMap[pre - k];
                }
                myMap[pre] += 1;
            }
            return count;
        }
    };
    ```
14. - isdigit(a):判断a是否为数字
    - isalpha(a):判断a是否为字母，包括大小写
    - atoi(a.c_str())返回字符串a转变的int
15. unordered_map不能用sort排序。
16. 任务调度器：力扣621
    给你一个用字符数组 tasks 表示的 CPU 需要执行的任务列表。其中每个字母表示一种不同种类的任务。任务可以以任意顺序执行，并且每个任务都可以在 1 个单位时间内执行完。在任何一个单位时间，CPU 可以完成一个任务，或者处于待命状态。

    然而，两个 相同种类 的任务之间必须有长度为整数 n 的冷却时间，因此至少有连续 n 个单位时间内 CPU 在执行不同的任务，或者在待命状态。

    你需要计算完成所有任务所需要的 最短时间 。
    解题：
    - count表示最终要花的时间，length表示tasks的长度。count >= length。
    - 用大小为26的vector<int> nums记录tasks个数，然后降序sort。从个数最多的task开始(个数记为max_num)，需要的时间为（max_num - 1）* (n + 1) + 1，其中包含空位。
    - 开始放后面的task，若还有空位，且task的个数与max_num相等，count++，task的个数小于max_num，直接放空位里；若没有空位，count = length。
    - result = max（count, length）
  17. vector<int>降序sort: sort(v.begin(), v.end(), greater<int>());//不要忘记greater<int>后面的括号。
  18. 运算符优先级：（从上到下，优先级递增）
        - ()、[]
        - !、++、--、(正)+、(负)-
        - *、/、%
        - +、-
        - \>>、<<
        - \>、<、>=、<=
        - ==、!=
        - &
        - ^
        - |
        - &&
        - ||
        - ?:
        - =、以及各种运算加等号例(+=)
  19. int最值：INT_MIN, INT_MAX
  20. 数组可直接通过==比较。
  21. 访问字典中不存在的键会返回值类型的0值。
  22. 动态规划：  
    - 有什么状态？  
    - 怎么达到这个状态？  
    - 由谁决定？
  23. vector insert，o(n)
    用法：insert(迭代器i，元素)，在迭代器i前插入元素 


