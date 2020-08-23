---
title:  "알고리즘 - DFS"
excerpt: "DFS, Backtracking"
header:

categories:
  - Algorithm
tags:
  - algorithm
  - backtracking
  - DFS
last_modified_at: 2020-08-24 03:50:00

---

# 알고리즘 학습정리



## DFS

- [14503 - 로봇 청소기](https://www.acmicpc.net/problem/14503)

- 핵심 키워드 : `DFS`, `백트래킹`, `좌표탐색`

- 좌표계 탐색과 DFS를 연습할 수 있는 좋은 문제이다.

- 로봇이 어떤 방식으로 움직이는지 정확히 문제를 파악하는 것이 중요하다.

  ![map](https://user-images.githubusercontent.com/58318041/90986352-2cb72080-e5bd-11ea-8fd2-d34e912bb141.png)



## 실수 포인트

- 방향을 좌표로 표현할 때 북(위쪽) 방향이 -1임을 인지해야한다.

- 현재 방향에서 원하는 방향을 다음과 같이 간단히 표현할 수 있다. (0부터 시작하지 않아도 전 방향을 탐색 할 수 있다.)

  ```java
  // direction : N E S W
  static int dx[] = {0, 1, 0, -1};
  static int dy[] = {-1, 0, 1, 0};
  
  // left
  int direction = (direction + 3) % 4;
  // back
  int back = (direction + 2) % 4;
  ```

- 2차원 배열에서 `map[x][y]`가 아니고 `map[y][x]`의 표현이 옳다.

- 이 문제의 경우 완전탐색이 아니기떄문에 로봇이 탐색을 끝냈다면 종료조건을 추가해주어야 한다. 그렇지 않으면 이전 노드로 돌아와 탐색을 시작하기 떄문이다. (로봇이 탐색하지 않은 곳으로 순간이동 하는 것과 같다.)

  ```java
  if (nx >= 0 && nx < M && ny >= 0 && ny < N) {
    if (map[ny][nx] == 0) {
      map[ny][nx] = 2;
      dfs(nx, ny, direction, map);
      // 완전 탐색을 하면 안되기 때문에 해당 조건을 추가한다.
      // return 하지 않으면 로봇이 탐색하지 않은 영역으로 순간이동한다.
      return map;
    }
  }
  ```

  

## 풀이

- 로봇이 네 방향을 모두 탐색 후 후진하는 경우를 구현하지 못해 블로그를 참고하여 풀었다.

- 나는 DFS로 풀이하였지만, 단순 구현으로 풀이한 사람도 있었다.

  ```java
  import java.io.BufferedReader;
  import java.io.IOException;
  import java.io.InputStreamReader;
  import java.util.StringTokenizer;
  
  public class Main {
  
      // direction : N E S W
      static int dx[] = {0, 1, 0, -1};
      static int dy[] = {-1, 0, 1, 0};
      static int N;
      static int M;
  
      public static void main(String[] args) throws IOException {
          // init
          BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
          StringTokenizer st = new StringTokenizer(br.readLine());
  
          // array size
          N = Integer.parseInt(st.nextToken());
          M = Integer.parseInt(st.nextToken());
          int[][] map = new int[N][M];
  
          // x, y coordinate & direction
          st = new StringTokenizer(br.readLine());
          int row = Integer.parseInt(st.nextToken());
          int column = Integer.parseInt(st.nextToken());
          int direction = Integer.parseInt(st.nextToken());
  
          // array map
          for (int i = 0; i < N; i++) {
              st = new StringTokenizer(br.readLine());
              for (int j = 0; j < M; j++) {
                  map[i][j] = Integer.parseInt(st.nextToken());
              }
          }
  
          // start cleaning
          map = dfs(column, row, direction, map);
          System.out.println(countCleanArea(map));
      }
  
      /**
       * cleaning the map
       * 1. 모든 방향을 확인하며 청소 가능한 방향을 탐색 후 dfs 재귀 호출
       * 2. 모든 방향의 청소가 끝났다면 후진
       * 3. 후진이 불가능하면 종료
       */
      public static int[][] dfs(int x, int y, int direction, int[][] map) {
          map[y][x] = 2;
          printMap(map);
  
          for (int i = 0; i < 4; i++) {
              // make current direction left
              direction = (direction + 3) % 4;
              int nx = x + dx[direction];
              int ny = y + dy[direction];
  
              if (nx >= 0 && nx < M && ny >= 0 && ny < N) {
                  if (map[ny][nx] == 0) {
                      map[ny][nx] = 2;
                      dfs(nx, ny, direction, map);
                      // 완전 탐색을 하면 안되기 때문에 해당 조건을 추가한다.
                      // return 하지 않으면 로봇이 탐색하지 않은 영역으로 순간이동한다.
                      return map;
                  }
              }
          }
  
          // 모든 방향의 청소가 끝났다면 후진
          // make current direction back
          int back = (direction + 2) % 4;
          int nx = x + dx[back];
          int ny = y + dy[back];
          if (nx >= 0 && nx < M && ny >=0 && ny < N) {
              if (map[ny][nx] != 1) {
                  dfs(nx, ny, direction, map);
              }
          }
          // 모든 방향의 청소가 끝나고 후진이 불가하면 종료
          return map;
      }
  
      public static int countCleanArea(int[][] map) {
          int count = 0;
          for (int i = 0; i < N; i++) {
              for (int j = 0; j < M; j++) {
                  if (map[i][j] == 2) {
                      count++;
                  }
              }
          }
          return count;
      }
  
      public static void printMap(int[][] map) {
          for (int i = 0; i < N; i++) {
              for (int j = 0; j < M; j++) {
                  System.out.print(map[i][j] + " ");
              }
              System.out.println();
          }
          System.out.println("========================");
      }
  }
  ```



### Reference

- [https://hyeooona825.tistory.com/56](https://hyeooona825.tistory.com/56)
- 그림 출처 : [https://zoonvivor.tistory.com/130](https://zoonvivor.tistory.com/130)

