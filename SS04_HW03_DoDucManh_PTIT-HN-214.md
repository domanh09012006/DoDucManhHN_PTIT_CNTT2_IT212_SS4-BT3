# BÀI 3: Đọc hiểu & Dò lỗi qua Prompt (Phát hiện lỗi logic lặp)

## 1. Phân tích lý do Prompt thô dễ bỏ sót lỗi

Prompt:

```text
Mã này bị lỗi gì?
```

Prompt trên quá chung chung nên AI thường chỉ kiểm tra cú pháp hoặc khả năng biên dịch của chương trình.

Các nguyên nhân khiến AI dễ bỏ sót lỗi logic:

- Không yêu cầu AI đóng vai trò **Code Auditor** hoặc **Code Reviewer**.
- Không cung cấp ca kiểm thử cụ thể để kiểm tra hành vi của chương trình.
- Không yêu cầu phân tích từng vòng lặp và giá trị của biến `i`, `j`.
- Không yêu cầu đánh giá độ đúng của thuật toán hoặc độ phức tạp.
- Vì chương trình không có lỗi biên dịch nên AI có thể kết luận mã nguồn "chạy được" mà không phát hiện lỗi logic.

Ví dụ với mảng `{1, 2, 3, 4}`, hàm đáng lẽ phải trả về `null` vì không có phần tử trùng. Tuy nhiên do vòng lặp trong bắt đầu từ `j = i`, điều kiện `arr[i] == arr[j]` luôn đúng ở lần so sánh đầu tiên (`arr[0] == arr[0]`), nên hàm luôn trả về `1`.

---

## 2. Prompt tối ưu mới

```text
Role:
Bạn là một Code Auditor và Java Algorithm Reviewer.

Goal:
Hãy phân tích đoạn mã Java dưới đây, tìm lỗi logic và đề xuất giải pháp tối ưu hơn.

Context:
Đây là hàm tìm phần tử trùng lặp đầu tiên trong mảng số nguyên.

Ca kiểm thử:

int[] arr = {1, 2, 3, 4};

Kết quả mong muốn:
null

Kết quả thực tế:
1

Constraint:
- Phân tích nguyên nhân gây lỗi.
- Giải thích vì sao vòng lặp hiện tại sai.
- So sánh độ phức tạp của thuật toán.
- Viết lại thuật toán sử dụng HashSet.
- Giảm độ phức tạp từ O(N²) xuống O(N).
- Giữ nguyên kiểu dữ liệu đầu vào và đầu ra.

Format:
1. Phân tích lỗi.
2. Giải pháp.
3. Mã nguồn Java hoàn chỉnh.
4. Giải thích ngắn gọn.
```

---

## 3. Mã nguồn Java đã sửa sử dụng HashSet

```java
import java.util.HashSet;
import java.util.Set;

public class DuplicateFinder {

    public static Integer findDuplicate(int[] arr) {

        if (arr == null || arr.length == 0) {
            return null;
        }

        Set<Integer> visited = new HashSet<>();

        for (int number : arr) {

            if (visited.contains(number)) {
                return number;
            }

            visited.add(number);
        }

        return null;
    }

    public static void main(String[] args) {

        int[] arr1 = {1, 2, 3, 4};
        int[] arr2 = {5, 8, 2, 8, 10};

        System.out.println(findDuplicate(arr1)); // null
        System.out.println(findDuplicate(arr2)); // 8
    }
}
```

### Giải thích

Phiên bản mới sử dụng `HashSet` để lưu các phần tử đã xuất hiện. Khi duyệt mảng, nếu một phần tử đã tồn tại trong `HashSet` thì đó chính là phần tử trùng lặp đầu tiên và được trả về ngay.

Thuật toán mới chỉ cần duyệt mảng một lần nên có độ phức tạp **O(N)**, tốt hơn so với thuật toán cũ sử dụng hai vòng lặp lồng nhau có độ phức tạp **O(N²)**.