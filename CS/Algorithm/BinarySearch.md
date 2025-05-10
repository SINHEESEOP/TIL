# 이분탐색 (Binary Search) 🔍

## 이분탐색이란?
- 정렬된 배열에서 특정 값을 찾는 알고리즘
- 시간 복잡도: O(log N)
- 공간 복잡도: O(1)

## 동작 방식
1. 배열의 중간값을 선택
2. 찾고자 하는 값과 중간값을 비교
3. 중간값이 더 크면 왼쪽 부분 탐색
4. 중간값이 더 작으면 오른쪽 부분 탐색
5. 값을 찾거나 탐색 범위가 없어질 때까지 반복

## 구현 예시
```java
public class BinarySearch {
    public static int binarySearch(int[] arr, int target) {
        int left = 0;
        int right = arr.length - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;  // 오버플로우 방지
            
            if (arr[mid] == target) {
                return mid;
            } else if (arr[mid] > target) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        
        return -1;  // 값을 찾지 못한 경우
    }
    
    // 재귀를 이용한 구현
    public static int binarySearchRecursive(int[] arr, int target, int left, int right) {
        if (left > right) {
            return -1;
        }
        
        int mid = left + (right - left) / 2;
        
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] > target) {
            return binarySearchRecursive(arr, target, left, mid - 1);
        } else {
            return binarySearchRecursive(arr, target, mid + 1, right);
        }
    }
}
```

## 이분탐색의 장점
- 빠른 검색 속도 (O(log N))
- 메모리 효율적
- 구현이 비교적 간단

## 이분탐색의 단점
- 정렬된 배열에서만 사용 가능
- 정렬에 추가 시간이 필요할 수 있음

## 활용 사례
- 정렬된 배열에서 특정 값 찾기
- 최적화 문제 해결
- 게임에서 점수 계산
- 데이터베이스 인덱싱

## 주의사항
- 정렬이 되어있지 않은 경우 사용 불가
- 중복된 값이 있는 경우 추가 처리 필요
- 오버플로우 주의 (left + right 연산 시) 