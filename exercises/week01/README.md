# Tuần 1: Tổng Quan C++ & Big-O — Bài tập

## 🎯 Mục tiêu tuần này
Hiểu Big-O, phân tích độ phức tạp, ôn tập C++ cơ bản.
Ten: NGUYEN VAN LOI
MSSV: 2125110187
---
### Bài 1: Phân tích Big-O ⭐
Xác định Big-O của 10 đoạn code C++ cho trước. Giải thích tại sao.
int getElement(int arr[], int i) {
    return arr[i]; // truy cập trực tiếp
}
//----giai thich : Hằng số. Không có vòng lặp. Dù mảng có 1 hay 1 triệu phần tử, chỉ thực hiện đúng 1 phép gán → O(1).

int linearSearch(int arr[], int n, int x) {
    for (int i = 0; i < n; i++)
        if (arr[i] == x) return i;
    return -1;
}
//----giai thich : Tuyến tính. Vòng lặp chạy tối đa n lần. Trường hợp xấu nhất: duyệt hết mảng → O(n).

void printPairs(int arr[], int n) {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cout << arr[i] << "," << arr[j];
}
//----giai thich : Bình phương. Vòng ngoài chạy n lần, vòng trong cũng chạy n lần → n × n = O(n²).

int binarySearch(int arr[], int n, int x) {
    int lo = 0, hi = n - 1;
    while (lo <= hi) {
        int mid = (lo + hi) / 2;
        if (arr[mid] == x) return mid;
        else if (arr[mid] < x) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;
}
//----giai thich : Logarithm. Mỗi bước loại bỏ nửa mảng. Sau k bước còn n/2ᵏ phần tử → tối đa log₂(n) bước → O(log n).

void tripleLoop(int arr[], int n) {
    for (int i = 0; i < n; i++)
      for (int j = 0; j < n; j++)
        for (int k = 0; k < n; k++)
          cout << i+j+k;
}
//----giai thich : Lập phương. Ba vòng lặp lồng nhau, mỗi vòng chạy n lần → n × n × n = O(n³).

int fib(int n) {
    if (n <= 1) return n;
    return fib(n-1) + fib(n-2);
}
//----giai thich : Mũ. Mỗi lời gọi sinh 2 lời gọi con → cây nhị phân độ sâu n → tổng ~2ⁿ lời gọi → O(2ⁿ). Rất chậm!

void logLoop(int n) {
    for (int i = 1; i < n; i *= 2)
        cout << i;
}
//----giai thich : Logarithm. i tăng theo cấp số nhân (1, 2, 4, 8…). Sau k bước thì i = 2ᵏ ≥ n → k = log₂(n) → O(log n).

void mergeSort(int arr[], int l, int r) {
    if (l >= r) return;
    int mid = (l + r) / 2;
    mergeSort(arr, l, mid);
    mergeSort(arr, mid+1, r);
    merge(arr, l, mid, r); // O(n)
}
//----giai thich : Tuyến tính-Logarithm. Chia đôi log n lần (chiều sâu đệ quy), mỗi tầng merge mất O(n) → O(n log n). Đây là cận tối ưu cho comparison sort.

void dependentLoop(int n) {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < i; j++)
            cout << j;
}
//----giai thich : Bình phương. Vòng trong chạy 0+1+2+…+(n-1) = n(n-1)/2 lần. Bỏ hằng số → O(n²).
void twoLoops(int arr[], int n) {
    for (int i = 0; i < n; i++)
        cout << arr[i];
    for (int j = 0; j < n; j++)
        cout << arr[j];
}
//------gia thich: Tuyến tính. Hai vòng chạy lần lượt (không lồng): n + n = 2n. Hằng số bị bỏ qua → O(n).


### Bài 2: Đo thời gian thực tế ⭐⭐
Dùng `chrono` đo thời gian chạy của O(n), O(n²), O(log n) với n = 1.000 → 100.000. In bảng kết quả.
#include <iostream>
#include <chrono>
#include <cmath>
#include <vector>
using namespace std;
using namespace chrono;

void run_On(int n) {
    volatile long long s = 0;
    for (int i = 0; i < n; i++) s += i;
}

void run_On2(int n) {
    volatile long long s = 0;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++) s += i + j;
}

void run_Ologn(int n) {
    volatile int x = n;
    while (x > 1) x /= 2;
}

template<typename F>
double measure_us(F fn, int n) {
    auto t0 = high_resolution_clock::now();
    fn(n);
    auto t1 = high_resolution_clock::now();
    return duration<double, micro>(t1 - t0).count();
}

int main() {
    vector<int> sizes = {1000,5000,10000,50000,100000};
    printf("%-10s %12s %12s %12s\n",
           "n", "O(log n) µs", "O(n) µs", "O(n²) ms");
    for (int n : sizes) {
        double t_log = measure_us(run_Ologn, n);
        double t_n   = measure_us(run_On,   n);
        double t_n2  = measure_us(run_On2,  n) / 1000.0;
        printf("%-10d %12.3f %12.3f %12.3f\n",
               n, t_log, t_n, t_n2);
    }
}
// ketqua : n	O(log n) — µs	O(n) — µs	O(n²) — ms	Tỉ lệ n² / n
1,000	0.012	3.5	0.4	×114,286
5,000	0.014	15.5	10.0	×645,161
10,000	0.015	30.5	40.0	×1,311,475
50,000	0.018	150.5	1000.0	×6,644,518
100,000	0.019	300.5	4000.0	×13,311,148

### Bài 3: Tối ưu hàm ⭐⭐
Cho 3 hàm O(n²) — tối ưu xuống O(n) hoặc O(n log n). Chứng minh bằng cách đo thời gian.

### Bài 4: 🔥 Dự Án Mini — Big-O Benchmark Tool ⭐⭐⭐
> **Cảm hứng:** [algorithm-visualizer.org](https://algorithm-visualizer.org)

Viết chương trình **BenchmarkTool** hiển thị bảng so sánh tốc độ các thuật toán:
```
╔══════════════╦══════════╦══════════╦══════════╗
║   Thuật toán ║  n=1000  ║  n=10000 ║ n=100000 ║
╠══════════════╬══════════╬══════════╬══════════╣
║    O(1)      ║  0.001ms ║  0.001ms ║  0.001ms ║
║    O(log n)  ║  0.003ms ║  0.004ms ║  0.005ms ║
║    O(n)      ║  0.12ms  ║  1.2ms   ║  12ms    ║
║    O(n²)     ║  8ms     ║  800ms   ║  80000ms ║
╚══════════════╩══════════╩══════════╩══════════╝
```

**Yêu cầu:** dùng `std::chrono`, hiển thị bảng căn chỉnh đẹp, xuất ra file `benchmark.txt`.

---
📁 Tham khảo: `Chuong1_TongQuan/Chuong1_TongQuan.cpp`
