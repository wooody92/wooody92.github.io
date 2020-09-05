---
title:  "알고리즘 - Backtracking"
excerpt: "완전탐색 : DFS, Backtracking"
header:

categories:
  - Algorithm
tags:
  - algorithm
  - 완전탐색
  - backtracking
  - DFS
last_modified_at: 2020-08-21 19:20:00

---

<br>

# 알고리즘 학습정리



## 완전 탐색 : Backtracking (백트래킹)

- [14502 - 연구소](https://www.acmicpc.net/problem/14502)

- 핵심 키워드 : `완전탐색`, `DFS`, `백트래킹`, `좌표탐색`

- 2차원 배열의 경우 전체 인덱스를 아래와 같이 탐색 할 수 있다.

  ```java
  for (int i = start; i < column * row; i++) {
    int x = i / row;
    int y = i % row;
  
    map[x][y] = 1;
    setWall(i + 1, depth + 1, map);
    map[x][y] = 0;
  }
  ```



## 풀이

- 1단계 : 백트래킹의 기본 구조

  ```java
  public class Main {
  
      public static void main(String[] args) throws IOException {
          // init
          BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
          StringTokenizer st = new StringTokenizer(br.readLine());
          int column = Integer.parseInt(st.nextToken());
          int row = Integer.parseInt(st.nextToken());
          int[][] map = new int[column][row];
  
          for (int i = 0; i < column; i++) {
              st = new StringTokenizer(br.readLine());
              for (int j = 0; j < row; j++) {
                  map[i][j] = Integer.parseInt(st.nextToken());
              }
          }
  
          // logic
          setWall(0, 0, map);
      }
  
      /**
       * back-tacking
       */
      public static void setWall(int start, int depth, int[][] map) {
          int column = map.length;
          int row = map[0].length;
  
          if (depth == 3) {
              // base case
              print(map);
              System.out.println();
              return;
          }
  
          for (int i = start; i < column * row; i++) {
              int x = i / row;
              int y = i % row;
  
              map[x][y] = 1;
              setWall(i + 1, depth + 1, map);
              map[x][y] = 0;
          }
      }
  
      public static void print(int[][] map) {
          for (int i = 0; i < map.length; i++) {
              for (int j = 0; j < map[i].length; j++) {
                  System.out.print(map[i][j]);
              }
              System.out.println();
          }
      }
  }
  ```

  ```java
  // input
  4 6
  0 2 0 2 0 0
  1 0 0 0 0 2
  1 1 1 0 0 2
  0 0 0 0 0 2
    
  // output
  111000
  100002
  111002
  000002
  
  110100
  100002
  111002
  000002
  
  110010
  100002
  111002
  000002
  
  ...
    
  000000
  000000
  000000
  001011
  
  000000
  000000
  000000
  000111
  ```

- 2단계 : 바이러스가 퍼지는 메서드를 추가한 최종 코드

  ```java
  public class Main {
  
      public static class Point {
          int x;
          int y;
  
          public Point(int x, int y) {
              this.x = x;
              this.y = y;
          }
      }
  
      static List<Point> virusList = new ArrayList<Point>();
      static int[] dx = {1, -1, 0, 0};
      static int[] dy = {0, 0, 1, -1};
      static int max = 0;
  
      /**
       * 0 : safety
       * 1 : wall
       * 2 : virus
       */
      public static void main(String[] args) throws IOException {
          // init
          BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
          StringTokenizer st = new StringTokenizer(br.readLine());
          int column = Integer.parseInt(st.nextToken());
          int row = Integer.parseInt(st.nextToken());
          int[][] map = new int[column][row];
  
          for (int i = 0; i < column; i++) {
              st = new StringTokenizer(br.readLine());
              for (int j = 0; j < row; j++) {
                  map[i][j] = Integer.parseInt(st.nextToken());
                  if (map[i][j] == 2) {
                      virusList.add(new Point(i, j));
                  }
              }
          }
  
          // logic
          setWall(0, 0, map);
  
          // print max value of safety area : 0
          System.out.println(max);
      }
  
      /**
       * 완전 탐색 main logic
       */
      public static void setWall(int start, int depth, int[][] map) {
          int column = map.length;
          int row = map[0].length;
  
          // base case
          if (depth == 3) {
              // wall이 세워지고, virus 퍼지기 전 상태의 map 복사한다.
              int[][] copyMap = copyMap(map);
  
              // virus : 2가 있는 좌표를 순회하며 virust 퍼트린다.
              for (Point point : virusList) {
                  copyMap = spreadVirus(point.x, point.y, copyMap);
              }
  
              // virus 퍼진 맵의 safety 갯수의 max 값만 저장한다.
              max = Math.max(max, countSafety(copyMap));
  
              // 전과 후 비교를 위한 print
              print(map);
              System.out.println();
              print(copyMap);
              System.out.println("==========");
              return;
          }
  
          // back-tracking
          for (int i = start; i < column * row; i++) {
              int x = i / row;
              int y = i % row;
  
              if (map[x][y] == 0) {
                  map[x][y] = 1;
                  setWall(i + 1, depth + 1, map);
                  map[x][y] = 0;
              }
          }
      }
  
      // DFS
      public static int[][] spreadVirus(int x, int y, int[][] map) {
          int column = map.length;
          int row = map[0].length;
  
          for (int i = 0; i < 4; i++) {
              int nx = x + dx[i];
              int ny = y + dy[i];
  
              // 좌표가 정의된 배열의 범위 안의 경우만 탐색한다.
              if (0 <= nx && nx < column && 0 <= ny && ny < row) {
                  if (map[nx][ny] == 0) {
                      map[nx][ny] = 2;
                      spreadVirus(nx, ny, map);
                  }
              }
          }
          return map;
      }
  
      public static int[][] copyMap(int[][] map) {
          int[][] copyMap = new int[map.length][];
          for (int i = 0; i < map.length; i++) {
              copyMap[i] = map[i].clone();
          }
          return copyMap;
      }
  
      public static int countSafety(int[][] map) {
          int count = 0;
          for (int i = 0; i < map.length; i++) {
              for (int j = 0; j < map[i].length; j++) {
                  if (map[i][j] == 0) {
                      count++;
                  }
              }
          }
          return count;
      }
  
      public static void print(int[][] map) {
          for (int i = 0; i < map.length; i++) {
              for (int j = 0; j < map[i].length; j++) {
                  System.out.print(map[i][j]);
              }
              System.out.println();
          }
      }
  }
  ```

  ```java
  // input
  4 6
  0 2 0 2 0 0
  1 0 0 0 0 2
  1 1 1 0 0 2
  0 0 0 0 0 2
    
  // output
  121210
  100002
  111002
  000002
  
  121212
  122222
  111222
  222222
  ==========
  121201
  100002
  111002
  000002
  
  121221
  122222
  111222
  222222
  ==========
  ...
  ==========
  020200
  100002
  111002
  010112
  
  222222
  122222
  111222
  010112
  ==========
  020200
  100002
  111002
  001112
  
  222222
  122222
  111222
  001112
  ==========
  5
  ```

  

### Reference

- [https://bcp0109.tistory.com/25](https://bcp0109.tistory.com/25)

