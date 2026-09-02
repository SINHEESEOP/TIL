# 투포인터 (Two Pointers)

## 투포인터란?
- 배열이나 리스트에서 두 개의 포인터를 사용하여 문제를 해결하는 알고리즘
- 시간 복잡도: O(N)
- 공간 복잡도: O(1)

## 동작 방식
1. 두 개의 포인터를 배열의 시작점이나 끝점에 위치
2. 문제의 조건에 따라 포인터들을 이동
3. 포인터들의 위치를 조정하며 원하는 조건을 만족하는 경우를 찾음

## 구현 예시
```java
public class TwoPointers {
    // 두 수의 합 구하기
    public static int[] twoSum(int[] arr, int target) {
        int left = 0;
        int right = arr.length - 1;
        
        while (left < right) {
            int sum = arr[left] + arr[right];
            
            if (sum == target) {
                return new int[]{left, right};
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        
        return new int[]{-1, -1};  // 합이 target인 두 수를 찾지 못한 경우
    }
    
    // 연속된 부분 수열의 합
    public static int[] findSubarraySum(int[] arr, int target) {
        int left = 0;
        int right = 0;
        int currentSum = 0;
        
        while (right < arr.length) {
            currentSum += arr[right];
            
            while (currentSum > target && left < right) {
                currentSum -= arr[left];
                left++;
            }
            
            if (currentSum == target) {
                return new int[]{left, right};
            }
            
            right++;
        }
        
        return new int[]{-1, -1};  // 합이 target인 부분 수열을 찾지 못한 경우
    }
}
```

## 투포인터의 장점
- 선형 시간 복잡도 (O(N))
- 추가 메모리 사용이 적음
- 구현이 직관적

## 투포인터의 단점
- 정렬이 필요한 경우가 많음
- 특정 문제에만 적용 가능

## 활용 사례
- 두 수의 합 구하기
- 세 수의 합 구하기
- 연속된 부분 수열의 합
- 회문 검사
- 중복 제거

## 주의사항
- 포인터 이동 조건을 정확히 설정해야 함
- 배열의 경계 조건 처리 필요
- 정렬이 필요한 경우 정렬 시간 고려 