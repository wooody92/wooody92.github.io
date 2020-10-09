---
title:  "Effective Java #9 - try with resources"
excerpt: "Item 9 : try-finally 보다는 try-with-resources를 사용하라"
header:

categories:
  - Effective Java
tags:
  - Java
  - Effective Java
  - try with resources
last_modified_at: 2020-10-09. 17:10:00

---

<br>

# 객체 생성과 파괴 (2장)

## #9 : try-finally 보다는 try-with-resources를 사용하라

> 회수해야 하는 자원을 다룰 때는 예외없이 try-with-resources 방식을 사용하자.

### 개요

- 자바 라이브러리에는 `InputStream`, `OutputStream`, `java.sql.Connection`과 같이 `close()` 메서드를 호출하여 직접 닫아줘야 하는 자원이 많다.



### try-finally

- 이 방식보다는 try-with-resources 방식을 사용하자.

- try-finally 방식은 자원이 둘 이상인 경우 지저분해 진다.

- 예외가 둘 이상 발생하는 경우, 나중에 발생하는 예외가 앞의 예외를 덮어씌워 숨겨진 에러를 추적하기 힘들다.

  ```java
  public static String firstOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
      return br.readLine();
    } finally {
      br.close();
    }
  }
  ```

### try-with-resources

- try-with-resources 방식이 코드도 간결하고 숨겨진 에러를 추적하기도 쉽다.

- 아래는 위 try-finally 방식을 try-with-resources 방식으로 바꾼 코드이다.

  ```java
  public static String firstOfFile(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
      return br.readLine();
    }
  }
  ```

- 자원이 둘 이상일 경우의 코드이다.

  ```java
  public static void copy(String src, String dst) throws IOException {
    final int BUFFER_SIZE = 100;
    try (InputStream in = new FileInputStream(src); OutputStream out = new FileOutputStream(dst)) {
      byte[] buf = new byte[BUFFER_SIZE];
      int n;
      while ((n = in.read(buf)) >= 0) {
        out.write(buf, 0, n);
      }
    }
  }
  ```

- try-with-resources를 catch 절과 함께 사용하는 방식이다.

  ```java
  public static String firstOfFile3(String path) {
    String defaultValue = "";
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
      return br.readLine();
    } catch (IOException e) {
      return defaultValue;
    }
  }
  ```



### 핵심 정리

- 꼭 회수해야 하는 자원을 다룰 때는 예외없이 try-with-resources 방식을 사용하자.
- 코드는 더 간결하고 만들어지는 예외 정보도 훨씬 유용하다.



## References

- Effective Java 3/E - Joshua Bloch

