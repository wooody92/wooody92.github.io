---
title:  "알고리즘 - Comparator"
excerpt: "Comparator, compareTo"
header:

categories:
  - Algorithm
tags:
  - algorithm
  - Comparator
  - compareTo
last_modified_at: 2020-09-05 20:50:00

---

<br>

# 알고리즘 학습정리



## Comparator와 compareTo

- [17686 - 파일명 정렬](https://programmers.co.kr/learn/courses/30/lessons/17686)
- 핵심 키워드 : `Comparator`, `compareTo`, `정규식`
- `Comparator` 인터페이스를 연습 할 수 있는 문제였다.
- `Comparator`는 인터페이스이고, `compareTo`는 비교기 메서드이다.



## 포인트

- `FileInfo` 클래스를 만들어서 객체지향적으로 필요한 데이터를 정리했다.

- `FileInfo`의 필드값인 `head`와 `number`를 `parsing`하는 과정에서 정규표현식을 이용하였다.

- `head`값을 비교하고 오름차순으로 정렬한다. 단 `head`가 같은 경우에는 `number`를 기준으로 정렬한다.

  ```java
  // 비교 후 오름차순으로 정렬한다.
  Arrays.sort(fileInfos, (o1, o2) -> {
    if (o1.head.equals(o2.head)) {
      return o1.number.compareTo(o2.number);
    }
    return o1.head.compareTo(o2.head);
  });
  ```

  

## 풀이

- 전체 코드

  ```java
  import java.util.Arrays;
  
  /**
   * 1. FileInfo Class 만들고 정렬 기준에 맞추어 데이터 가공하여 저장
   * 2. 기준에 맞도록 정렬해서 해당 인덱스 저장
   * 3. 정렬된 인덱스 기준으로 files 정렬
   */
  public class Solution {
  
      class FileInfo {
  
          private int idx;
          private String head;
          private Integer number;
  
          public FileInfo(int idx, String head, Integer number) {
              this.idx = idx;
              this.head = head;
              this.number = number;
          }
      }
  
      public String[] solution(String[] files) {
  
          // init FileInfo class
          FileInfo[] fileInfos = new FileInfo[files.length];
          for (int i = 0; i < files.length; i++) {
              int numberIndex = getNumberIndex(files[i]);
              int tailIndex = getTailNumber(files[i], numberIndex);
              String head = files[i].substring(0, numberIndex);
              String number = files[i].substring(numberIndex, tailIndex);
              fileInfos[i] = new FileInfo(i, head.toLowerCase(), Integer.parseInt(number));
          }
  
          // sort by head. if same, then sort by number
          Arrays.sort(fileInfos, (o1, o2) -> {
              if (o1.head.equals(o2.head)) {
                  return o1.number.compareTo(o2.number);
              }
              return o1.head.compareTo(o2.head);
          });
  
          // result
          String[] result = new String[files.length];
          for (int i = 0; i < result.length; i++) {
              int sortedIndex = fileInfos[i].idx;
              result[i] = files[sortedIndex];
          }
          return result;
      }
  
      public int getNumberIndex(String file) {
          int numberIndex = -1;
          String regex = "[0-9]*$";
          String[] temp = file.split("");
          for (int i = 0; i < temp.length; i++) {
              if (temp[i].matches(regex)) {
                  numberIndex = i;
                  break;
              }
          }
          return numberIndex;
      }
  
      public int getTailNumber(String file, int numberIndex) {
          int tailIndex = file.length();
          String regex = "[0-9]*$";
          String[] temp = file.split("");
          for (int i = numberIndex; i < temp.length; i++) {
              if (!temp[i].matches(regex)) {
                  tailIndex = i;
                  break;
              }
          }
          return tailIndex;
      }
  
      public static void main(String[] args) {
          Solution solution = new Solution();
  //        String[] files = {"F-5 Freedom Fighter", "B-50 Superfortress", "A-10 Thunderbolt II", "F-14 Tomcat"};
          String[] files = {"img12.png", "img10.png", "img02.png", "img1.png", "IMG01.GIF", "img2.JPG"};
          solution.solution(files);
      }
  }
  ```

