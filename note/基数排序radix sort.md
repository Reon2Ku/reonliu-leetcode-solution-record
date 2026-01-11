# 基数排序 radix sort

- **稳定性**：基数排序是一种**稳定**的排序算法

- 

  **时间复杂度**：O(d * (n + k))，其中d是最大数字的位数，k是每位可能的取值个数（十进制下为10）。当d为常数时，复杂度近似为线性O(n)

- **空间复杂度**：O(n + k)，需要额外的空间来存放桶

基数排序是一种理论上的O(n)算法。当你绕不开排序又要求理论O(n)时要知道有基数排序这种东西的存在。基数排序的效率高度依赖于最大位数 `d`。当 `d`较小且 `k`不大时，它可能接近线性 O(n)。

基数排序的时间复杂度是 **O(d \* (n + k))**，其中 `n`是待排序元素个数，`d`是最大数字的位数（或字符串的最大长度），`k`是每一位可能的取值范围（例如十进制数字就是0-9，所以 `k=10`）

为什么要求基数排序一定要是稳定的排序？因为你要保证，在给低位排序完之后，他们的相对位置保持固定，不然继续拍高位会导致排序位置混乱。

~~~
#include <iostream>
#include <vector>
#include <algorithm> // 用于max_element

// 使用计数排序作为子程序，对特定位进行排序
void countingSortForRadix(std::vector<int>& arr, int exp) {
    int n = arr.size();
    std::vector<int> output(n); // 输出数组
    std::vector<int> count(10, 0); // 计数数组，初始化为0

    // 统计每个数字（0-9）出现的次数
    for (int i = 0; i < n; i++) {
        int digit = (arr[i] / exp) % 10;
        count[digit]++;
    }

    // 将count[i]变为小于等于i的元素个数（计算前缀和）
    for (int i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }

    // 从后往前遍历原数组，保证稳定性
    for (int i = n - 1; i >= 0; i--) {
        int digit = (arr[i] / exp) % 10;
        output[count[digit] - 1] = arr[i];
        count[digit]--;
    }

    // 将排序好的output数组复制回原数组arr
    for (int i = 0; i < n; i++) {
        arr[i] = output[i];
    }
}

// 基数排序主函数
void radixSort(std::vector<int>& arr) {
    if (arr.empty()) return;

    // 找到数组中的最大值，以确定最大位数
    int maxVal = *std::max_element(arr.begin(), arr.end());

    // 从最低位（个位）开始，依次对每一位进行计数排序
    for (int exp = 1; maxVal / exp > 0; exp *= 10) {
        countingSortForRadix(arr, exp);
    }
}

// 示例代码用法
int main() {
    std::vector<int> arr = {170, 45, 75, 90, 802, 24, 2, 66};
    
    std::cout << "排序前: ";
    for (int num : arr) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    radixSort(arr);

    std::cout << "排序后: ";
    for (int num : arr) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    return 0;
}
~~~

- **最低位优先（LSD）**：从数字的最低位（个位）开始排序。
- **最高位优先（MSD）**：从数字的最高位开始排序。