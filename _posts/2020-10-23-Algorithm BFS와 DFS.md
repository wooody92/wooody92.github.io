---
title:  "알고리즘 - BFS와 DFS"
excerpt: "BFS와 DFS"
header:

categories:
  - Algorithm
tags:
  - algorithm
  - BFS
  - DFS
last_modified_at: 2020-10-23 15:30:00

---

<br>

# 알고리즘 학습정리



## # DFS와 BFS

- [1260 - DFS와 BFS](https://www.acmicpc.net/problem/1260)
- DFS와 BFS의 차이와 동작원리를 학습하는 좋은 문제이다.



### 문제

- 예제 입력과 인접행렬인 map으로 표현한 그림은 아래와 같다.

- 코드를 따라가며 동작 순서를 직접 작성해보는 것이 이해에 도움이 됐다.

- 풀이에는 인접행렬 방식과 인접리스트를 이용한 방식이 있는데, 인접행렬의 경우 정점의 갯수가 적을때만 사용한다.

  ```java
  4 5 1
  1 2
  1 3
  1 4
  2 4
  3 4
  ```

  ![bfs-01](https://user-images.githubusercontent.com/58318041/96960574-b0e63f80-153d-11eb-85d2-1f3c247cb002.jpeg)

  ![bfs-02](https://user-images.githubusercontent.com/58318041/96960606-c5c2d300-153d-11eb-8d2a-b6e6d0c6c7d2.jpeg)



## # BFS (Breadth First Search, 너비 우선 탐색)

- 현재 위치에 인접한 모든 위치의 노드를 방문하고, 그 이웃 노드들의 또 다른 이웃 노드들을 방문하는 것은 그 다음에 진행하는 것을 의미한다.

- 최단 경로 문제 시 사용한다.

  

### 코드

- BFS는 Queue 자료구조를 이용하여 구현한다.

  ```java
  public class Solution {
  
      static int[][] map; // 0으로 초기화
      static boolean[] visited;
      static int N; // 정점 갯수
      static int M; // 간선 갯수
  
      public static void main(String[] args) throws IOException {
          BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
          StringTokenizer st = new StringTokenizer(br.readLine());
          N = Integer.parseInt(st.nextToken());
          M = Integer.parseInt(st.nextToken());
          int startingPoint = Integer.parseInt(st.nextToken()); // 시작 정점
  
          // 의미상 편리성을 위해 0번은 사용하지 않는다.
          map = new int[N + 1][N + 1];
          visited = new boolean[N + 1];
  
          // map에 간선으로 연결되어 있는 정점 관계를 1로 마킹
          for (int i = 0; i < M; i++) {
              st = new StringTokenizer(br.readLine());
              int start = Integer.parseInt(st.nextToken());
              int end = Integer.parseInt(st.nextToken());
              map[start][end] = 1;
              map[end][start] = 1;
          }
          bfs(startingPoint);
      }
  
      static void bfs(int point) {
          Queue<Integer> queue = new LinkedList<>();
          queue.offer(point);
          visited[point] = true;
  
          while (!queue.isEmpty()) {
              int x = queue.poll();
              System.out.print(x + " ");
  
              // map을 순회하며 간선을 확인 후 연결되어 있는 정점을 queue에 입력하고 visited 마킹
              for (int i = 1; i <= N; i++) {
                  if (map[x][i] == 1 && visited[i] == false) {
                      queue.offer(i);
                      visited[i] = true; // 이미 방문한 정점은 다시 queue에 넣지 않기 위해 마킹한다.
                  }
              }
          }
      }
  }
  ```

  

## # DFS (Depth First Search, 깊이 우선 탐색)

- 루트 노트에서 시작해서 다음 분기로 넘어가기 전에 해당 노드가 갖고있는 자식노드를 완전하게 탐색하는 방법이다.
- 모든 노드를 탐색해야 할 경우 사용 할 수 있다. (완전 탐색 문제)
  - 미로 탐색 시 한 방향으로 계속 가다가 길이 없으면 다시 가까운 갈림길로 돌아와 다른 방향으로 탐색을 진행하는 방법과 유사하다.
- 재귀 또는 스택 자료구조를 사용하는 순환 알고리즘이다.



### 코드

- DFS는 Stack과 재귀를 이용하여 구현하였다.

  ```java
  public class Solution {
  
      static int[][] map; // 0으로 초기화
      static boolean[] visited;
      static int N; // 정점 갯수
      static int M; // 간선 갯수
  
      public static void main(String[] args) throws IOException {
          BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
          StringTokenizer st = new StringTokenizer(br.readLine());
          N = Integer.parseInt(st.nextToken());
          M = Integer.parseInt(st.nextToken());
          int startingPoint = Integer.parseInt(st.nextToken()); // 시작 정점
  
          // 의미상 편리성을 위해 0번은 사용하지 않는다.
          map = new int[N + 1][N + 1];
          visited = new boolean[N + 1];
  
          // map에 간선으로 연결되어 있는 정점 관계를 1로 마킹
          for (int i = 0; i < M; i++) {
              st = new StringTokenizer(br.readLine());
              int start = Integer.parseInt(st.nextToken());
              int end = Integer.parseInt(st.nextToken());
              map[start][end] = 1;
              map[end][start] = 1;
          }
          dfs2(startingPoint);
      }
  
      /**
       * stack
       */
      static void dfs(int point) {
          Stack<Integer> stack = new Stack<>();
          stack.push(point);
          visited[point] = true;
          System.out.print(point + " ");
  
          while (!stack.isEmpty()) {
              int x = stack.peek();
              boolean flag = false;
  
              for (int i = 1; i <= N; i++) {
                  if (map[x][i] == 1 && visited[i] == false) {
                      stack.push(i);
                      visited[i] = true;
                      System.out.print(i + " ");
                      flag = true;
                      break;
                  }
              }
              if (!flag) {
                  stack.pop();
              }
          }
      }
  
      /**
       * recursive
       */
      static void dfs2(int point) {
          visited[point] = true;
          System.out.print(point + " ");
  
          // map을 순회하며 간선을 확인 후 연결되어 있는 정점이 있다면 거기서부터 다시 시작
          for (int i = 0; i <= N; i++) {
              if (map[point][i] == 1 && visited[i] == false) {
                  dfs2(i);
              }
          }
      }
  }
  ```

  

## References

- [https://sundries-in-myidea.tistory.com/4](https://sundries-in-myidea.tistory.com/4)
- [https://take-her-to-the-moon-for-me.tistory.com/15](https://take-her-to-the-moon-for-me.tistory.com/15)
- [https://yunyoung1819.tistory.com/86](