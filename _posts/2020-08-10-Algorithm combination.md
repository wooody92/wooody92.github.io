---
title:  "알고리즘 - 조합(combination)"
excerpt: "Combination : recursive & backtracking"
header:

categories:
  - Algorithm
tags:
  - algorithm
  - combination
  - recursive
  - backtracking
  - 조합
  - 재귀
  - 백트래킹
last_modified_at: 2020-08-10 18:00:00

---

# 알고리즘 학습정리



## Combination (조합)

- `n`개의 숫자에서 `r`개를 순서에 상관없이 뽑는 경우를 말한다.

- `int[] arr = {1, 2, 3}`일 때 이 중 2개의 숫자를 조합으로 뽑는 경우 결과는 `{1, 2}`, `{1, 3}`, `{2, 3}`이다.

- `visited[i]`의 인덱스가 `true` 인 경우만 출력한다.

- 아래는 그림으로 도식화 해본 상태 공간 트리이다.

  ![combination](https://user-images.githubusercontent.com/58318041/89765383-2c387780-db31-11ea-858f-661f43d4ee69.jpeg)



### Recursive Combination (재귀)

- `base cae` : 뽑을 갯수가 0일 때 ( `r == 0`) 혹은 더이상 탐색할 인덱스가 없을 때 (`depth == n`)

- 특정 인덱스를 뽑는다면 `r`을 하나씩 줄인다. (3개의 숫자 중 2개를 뽑는 경우에 1개의 숫자를 뽑았다면 다음 경우는 2개의 숫자 중 1개를 뽑는 경우가 된다.)

- `java`코드에서 `debug` 찍고 도식화된 그림으로 순차적으로 따라가보면 이해가 된다.

  ```java
  /**
   * 재귀 사용
   * nCr : n개의 항목에서 r개를 뽑는다.
   * n : 전체 크기 혹은 길이
   * r : 뽑을 갯수
   * depth : 상태 공간 트리에서의 depth -> 현재 index를 의미
   */
  static void recursiveCombination(int[] arr, boolean[] visited, int depth, int n, int r) {
    if (r == 0) {
      print(arr, visited, n);
      return;
    }
    if (depth == n) {
      return;
    }
  
    visited[depth] = true;
    recursiveCombination(arr, visited, depth + 1, n, r - 1);
    visited[depth] = false;
    recursiveCombination(arr, visited, depth + 1, n, r);
  }
  ```



### Backtracking Combination (백트래킹)

- 전체 탐색 순서는 위 재귀 방식과 동일하다. (상태 공간 트리 참고)

- 반복문을 이용하여 구현하고, 만약 인덱스 0번의 값인 1을 뽑는다면 `visited[i]` 는 `true` 가 되고 뽑지 않는다면 `visited[i]` 는 `false` 이다.

- 1을 선택한 경우에 더이상 1은 고려 대상이 아니기 때문에 다음 `for` 문은 무조건 `i+1` 부터 시작해주어야 한다.

  ```java
  /**
   * 재귀 사용
   * nCr : n개의 항목에서 r개를 뽑는다.
   * n : 전체 크기 혹은 길이
   * r : 뽑을 갯수
   * depth : 상태 공간 트리에서의 depth -> 현재 index를 의미
   */
  static void backtrackingCombination(int[] arr, boolean[] visited, int depth, int n, int r) {
    if (r == 0) {
      print(arr, visited, n);
      return;
    }
  
    for (int i = depth; i < n; i++) {
      visited[i] = true;
      backtrackingCombination(arr, visited, i + 1, n, r - 1);
      visited[i] = false;
    }
  }
  ```



### 전체 코드

- 아래는 전체 코드와 출력된 결과값이다.

  ```java
  public class Combination {
  
      public static void main(String[] args) {
          int[] arr = {1, 2, 3};
          boolean[] visited = new boolean[arr.length];
          int n = arr.length;
          int r = 2;
  
          System.out.println("=====recursive=====");
          recursiveCombination(arr, visited, 0, n, r);
          System.out.println("=====backtracking=====");
          backtrackingCombination(arr, visited, 0, n, r);
      }
  
      /**
       * 재귀 사용
       * nCr : n개의 항목에서 r개를 뽑는다.
       * n : 전체 크기 혹은 길이
       * r : 뽑을 갯수
       * depth : 상태 공간 트리에서의 depth -> 현재 index를 의미
       */
      static void recursiveCombination(int[] arr, boolean[] visited, int depth, int n, int r) {
          if (r == 0) {
              print(arr, visited, n);
              return;
          }
          if (depth == n) {
              return;
          }
  
          visited[depth] = true;
          recursiveCombination(arr, visited, depth + 1, n, r - 1);
          visited[depth] = false;
          recursiveCombination(arr, visited, depth + 1, n, r);
      }
  
      /**
       * 백트래킹 사용
       * nCr : n개의 항목에서 r개를 뽑는다.
       * n : 전체 크기 혹은 길이
       * r : 뽑을 갯수
       */
      static void backtrackingCombination(int[] arr, boolean[] visited, int start, int n, int r) {
          if (r == 0) {
              print(arr, visited, n);
              return;
          }
  
          for (int i = start; i < n; i++) {
              visited[i] = true;
              backtrackingCombination(arr, visited, i + 1, n, r - 1);
              visited[i] = false;
          }
      }
  
      // 배열 출력
      static void print(int[] arr, boolean[] visited, int n) {
          for (int i = 0; i < n; i++) {
              if (visited[i]) {
                  System.out.print(arr[i] + " ");
              }
          }
          System.out.println();
      }
  }
  ```

  ```java
  // result
  =====recursive=====
  1 2 
  1 3 
  2 3 
  =====backtracking=====
  1 2 
  1 3 
  2 3 
  ```

  

### Reference

- [https://bcp0109.tistory.com/entry/%EC%A1%B0%ED%95%A9-Combination-Java?category=848939](https://bcp0109.tistory.com/entry/조합-Combination-Java?category=848939)
- https://limkydev.tistory.com/156

