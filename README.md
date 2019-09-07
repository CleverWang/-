# 单调栈总结

# 1. 单调栈

## 1.1 栈

* 算法中一种常见的数据结构，本质上是一种能够存储数据的容器，但是该容器只有一个开口，数据只能从该开口进和出。开口处的位置称作栈顶，最底端的位置称作栈底：

  ```c++
  栈结构：
       in --┑ ┍-→ out
            ↓ |
          |  9  | <- 栈顶
          |  7  |
          |  5  |
          |  1  |
          |  2  |
          |__3__| <- 栈底
  如果上面的全部出栈构成：{9, 7, 5, 1, 2, 3}
  ```
  
* 因此，数据的进出遵循”先进后出”（**FILO**，**F**irst **I**n **L**ast **O**ut）

## 1.2 单调栈

* 如果保持栈中的元素是单调的（单增或单减），那么称栈为单调栈

* 按栈中元素的单调性，可分为单调递增栈和单调递减栈：

  1. 单调递增栈

     ```c++
     单调递增栈：
             |  6  | <- 栈顶
             |  5  |
             |  4  |
             |  3  |
             |  2  |
             |__1__| <- 栈底
     从栈底到栈顶的元素单调递增
     比如：将元素3入栈，那么得将{6, 5, 4, 3}依次出栈，然后入栈3
             |  3  | <- 栈顶
             |  2  |
             |__1__| <- 栈底
     ```

     如果要入栈一个比栈顶元素小的值，得不断出栈直到栈顶元素小于待入栈元素

  2. 单调递减栈

     ```c++
     单调递减栈：
             |  1  | <- 栈顶
             |  2  |
             |  3  |
             |  4  |
             |  5  |
             |__6__| <- 栈底
     从栈底到栈顶的元素单调递减
     比如：将元素4入栈，那么得将{1, 2, 3, 4}依次出栈，然后入栈4
             |  4  | <- 栈顶 
             |  5  |
             |__6__| <- 栈底
     ```
     
     如果要入栈一个比栈顶元素大的值，得不断出栈直到栈顶元素大于待入栈元素

# 2. 应用

## 2.1 适用问题

* 由于单调栈特殊的单调性，单调栈可以用于解决这样的问题：给定一个数组a[n]，n为数组大小n>=0，求该数组每个元素右边的第一个比它大的元素，即对于数组中的一个元素a[i]，0<=i<=n-1，求a[j]，i<j<=n-1，使得a[i]<a[j]
* 这样的问题其实有4个，分别为：
  1. 寻找每个元素右边的第一个比它大的元素，记作：（右，大）
  2. 寻找每个元素右边的第一个比它小的元素，记作：（右，小）
  3. 寻找每个元素左边的第一个比它大的元素，记作：（左，大）
  4. 寻找每个元素左边的第一个比它小的元素，记作：（左，小）
* 接下来分别讨论

## 2.2 （右，大）

* 从一个例子出发：{3, 2, 3, 5, 4, 2, 1, 2, 4, 3}，类比站队

  ```c++
             ____________
            |  __________
       _____| |       |
      |  _| | |  _____| |
      | | | | | |  _| | |
      | | | | | | | | | |
      3 2 3 5 4 2 1 2 4 3 -> 数组
      5 3 5 * * 4 2 4 * * -> 结果
  ```

* 从结果我们可以发现，寻找一个元素右边的第一个比它大的元素，相当于向右画直线，直到碰到第一个比它高的。比如第一个元素3，向右画直线，碰到第四个元素5，那么结果就是5；第四个元素5向右画直线碰不到任何元素，则结果为无

* 寻找一个元素右边的第一个比它大的元素，我们得知道右边比它高的元素的情况，如果我们从后往前扫描数组，同时构建一个单调递减栈，能否解决问题呢？模拟分析一下：

  ```c++
  1.初始单调栈为空
      ---------------------
  栈顶                     |栈底 输出：* * * * * * * * * *
      ---------------------
  2.倒数第一个元素3准备入栈，由于单调栈为空，那么输出*，3入栈
      ---------------------
                         3|     输出：* * * * * * * * * *
      ---------------------
  3.倒数第二个元素4准备入栈，由于4大于栈顶元素3，要维持单调性，那么3出栈，栈为空，输出*，4入栈
      ---------------------
                         4|     输出：* * * * * * * * * *
      ---------------------
  4.倒数第三个元素2准备入栈，由于2小于栈顶元素4，单调性没有被破坏，那么栈顶即为结果，输出4，2入栈
      ---------------------
                       2 4|     输出：* * * * * * * 4 * *
      ---------------------
  5.倒数第四个元素1准备入栈，由于1小于栈顶元素2，单调性没有被破坏，那么栈顶即为结果，输出2，1入栈
      ---------------------
                     1 2 4|     输出：* * * * * * 2 4 * *
      ---------------------
  6.倒数第五个元素2准备入栈，由于2大于等于栈顶元素1和2，要维持单调性，那么1和2都出栈，直到遇到4为止，输出4，2入栈
      ---------------------
                       2 4|     输出：* * * * * 4 2 4 * *
      ---------------------
  7.同理
  ```

* 通过上面的分析可以发现，其实单调递减栈维持着一个当前元素向右看时能够看到的比它高的元素的集合，如果发现自己比栈顶前面几个人高，那么我向栈底走，直到碰到比自己高的人，同时栈顶前面的人都出栈，自己站在找到的位置上

* 编写代码如下：

  ```c++
  vector<int> nextGreater(const vector<int> &nums)
  {
      // 特殊情况处理
      int length = nums.size();
      if (length == 0)
          return {};
      else if (length == 1)
          return {-1};
  
      vector<int> res(length);
      stack<int> st; 
      // 从后往前扫描
      for (int i = length - 1; i >= 0; i--)
      {
          // 构建单调递减栈
          while (!st.empty() && st.top() <= nums[i])
              st.pop();
          res[i] = st.empty() ? -1 : st.top();
          st.push(nums[i]);
      }
      return res;
  }
  ```

* 总结一下，解决（右，大），需要从后往前构建单调递减栈，记为（<，-），<表示从后往前，-表示递减

## 2.3 （右，小）

- 同上面的例子

  ```c++
            |
            | |       |
      |   | | |       | |
      | | | | | |   | | |
      | | | | | | | | | |
      3 2 3 5 4 2 1 2 4 3 -> 数组
      2 1 2 4 2 1 * * 3 * -> 结果
  ```

- 寻找一个元素右边的第一个比它小的元素，同理，我们得知道右边比它矮的元素的情况，如果我们从后往前扫描数组，同时构建一个单调递增栈，能否解决问题呢？可以自己模拟一下

- 分析一下，其实单调递增栈维持着一个当前元素向右看时能够看到的比它矮的元素的集合，如果发现自己比栈顶前面几个人矮，那么我向栈底走，直到碰到比自己矮的人，同时栈顶前面的人都出栈，自己站在找到的位置上

- 编写代码如下：

  ```c++
  vector<int> nextSmaller(const vector<int> &nums)
  {
      // 特殊情况处理
      int length = nums.size();
      if (length == 0)
          return {};
      else if (length == 1)
          return {-1};
  
      vector<int> res(length);
      stack<int> st; 
      // 从后往前扫描
      for (int i = length - 1; i >= 0; i--)
      {
          // 构建单调递增栈
          while (!st.empty() && st.top() >= nums[i])
              st.pop();
          res[i] = st.empty() ? -1 : st.top();
          st.push(nums[i]);
      }
      return res;
  }
  ```

- 总结一下，解决（右，小），需要从后往前构建单调递增栈，记为（<，+）

## 2.4 （左，大）

- 同上面的例子

  ```c++
            |
            | |       |
      |   | | |       | |
      | | | | | |   | | |
      | | | | | | | | | |
      3 2 3 5 4 2 1 2 4 3 -> 数组
      * 3 * * 5 4 2 4 5 4 -> 结果
  ```

- 寻找一个元素左边的第一个比它大的元素，同理，我们得知道左边比它大的元素的情况，如果我们从前往后扫描数组，同时构建一个单调递减栈，能否解决问题呢？可以自己模拟一下

- 分析同（右，大）

- 编写代码如下：

  ```c++
  vector<int> previousGreater(const vector<int> &nums)
  {
      // 特殊情况处理
      int length = nums.size();
      if (length == 0)
          return {};
      else if (length == 1)
          return {-1};
  
      vector<int> res(length);
      stack<int> st; 
      // 从前往后扫描
      for (int i = 0; i < length; i++)
      {
          // 构建单调递减栈
          while (!st.empty() && st.top() <= nums[i])
              st.pop();
          res[i] = st.empty() ? -1 : st.top();
          st.push(nums[i]);
      }
      return res;
  }
  ```

- 总结一下，解决（左，大），需要从前往后构建单调递减栈，记为（>，-）

## 2.5 （左，小）

- 同上面的例子

  ```c++
            |
            | |       |
      |   | | |       | |
      | | | | | |   | | |
      | | | | | | | | | |
      3 2 3 5 4 2 1 2 4 3 -> 数组
      * * 2 3 3 * * 1 2 2 -> 结果
  ```

- 寻找一个元素左边的第一个比它小的元素，同理，我们得知道左边比它小的元素的情况，如果我们从前往后扫描数组，同时构建一个单调递增栈，能否解决问题呢？可以自己模拟一下

- 分析同（右，小）

- 编写代码如下：

  ```c++
  vector<int> previousSmaller(const vector<int> &nums)
  {
      // 特殊情况处理
      int length = nums.size();
      if (length == 0)
          return {};
      else if (length == 1)
          return {-1};
  
      vector<int> res(length);
      stack<int> st; 
      // 从前往后扫描
      for (int i = 0; i < length; i++)
      {
          // 构建单调递增栈
          while (!st.empty() && st.top() >= nums[i])
              st.pop();
          res[i] = st.empty() ? -1 : st.top();
          st.push(nums[i]);
      }
      return res;
  }
  ```

- 总结一下，解决（左，小），需要从前往后构建单调递增栈，记为（>，+）

# 3. 总结

1. 寻找每个元素右边的第一个比它大的元素（右，大），从后往前构建单调递减栈（<，-）
2. 寻找每个元素右边的第一个比它小的元素（右，小），从后往前构建单调递增栈（<，+）
3. 寻找每个元素左边的第一个比它大的元素（左，大），从前往后构建单调递减栈（>，-）
4. 寻找每个元素左边的第一个比它小的元素（左，小），从前往后构建单调递增栈（>，+）
