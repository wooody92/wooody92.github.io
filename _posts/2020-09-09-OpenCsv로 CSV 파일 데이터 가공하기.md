---
title:  "OpenCsv로 CSV 파일 데이터 가공하기"
excerpt: "OpenCsv로 파일 읽고 쓰기"
header:

categories:
  - Setup
tags:
  - OpenCsv
  - CSV
last_modified_at: 2020-09-09. 14:40:00

---

<br>

# OpenCSV

> `Java`의 `OpenCsv`를 사용하여 공공데이터 포탈의 `CSV` 파일을 원하는 데이터만 추출한 나만의 `CSV` 파일로 가공해보자.

## CSV

### 개요

- [공공데이터 포털](https://www.data.go.kr/data/15059995/fileData.do)
- 공공데이터 포털에서 얻은 서울시 상권 정보 `CSV` 파일에서 음식점 정보만 사용하고자 합니다.
- `CSV`파일의 용량이 크고(`180MB`) 데이터양이 많아(약 37만건) `OpenCsv`를 이용하여 데이터를 잘라내어 가공하려 합니다.

<br>

## OpenCsv

### OpenCsv로 CSV 파일 읽고 쓰기

- 실제 데이터를 가공하기 전에 `OpenCsv` 사용법을 연습해봅니다.

- `OpenCsv` 사용을 위해 `Gradle`의 경우 아래와 같이 의존성을 추가합니다.

  ```java
  compile group: 'com.opencsv', name: 'opencsv', version: '4.6'
  ```

- 아래는 `OpenCsv`를 이용하여 `CSV`파일을 생성하고 그 파일을 읽어보는 코드 입니다.

  ```java
  public class OpenCsv {
  
      public static void main(String[] args) throws IOException {
          writeCsvData("./sample.csv");
          readCsvData("./sample.csv");
      }
  
      private static void writeCsvData(String path) throws IOException {
          CSVWriter writer = new CSVWriter(new FileWriter(path));
  
          String[] category = {"name", "category", "city"};
          String[] data1 = {"피자맨", "피자", "병점"};
          String[] data2 = {"치킨맨", "치킨", "오산"};
          String[] data3 = {"타코맨", "타코", "영통"};
          String[] data4 = {"망고맨", "카페", "광교"};
  
          writer.writeNext(category);
          writer.writeNext(data1);
          writer.writeNext(data2);
          writer.writeNext(data3);
          writer.writeNext(data4);
          writer.close();
      }
  
      private static void readCsvData(String path) throws IOException {
          CSVReader reader = new CSVReader(new FileReader(path));
          String[] nextLine;
          while ((nextLine = reader.readNext()) != null) {
              for (int i = 0; i< nextLine.length; i++) {
                  System.out.print(nextLine[i] + " ");
              }
              System.out.println();
          }
      }
  }
  ```

- 위 코드를 실행해보면 아래와 같이 지정한 경로에 `sample.csv` 생성되고, 그 파일을 읽어 정상적으로 출력하는 것을 볼 수 있습니다.

  - 쓰기

  <img width="767" alt="csv-write" src="https://user-images.githubusercontent.com/58318041/92558514-ac293d00-f2a9-11ea-9dc1-e73bc044b0c6.png">
  
  - 읽기
  
  <img width="767" alt="csv-read" src="https://user-images.githubusercontent.com/58318041/92558526-b3504b00-f2a9-11ea-9196-5fe333d59a0a.png">



### OpenCsv로 데이터 가공하기 #1

- 위 예제를 바탕으로 실제 공공데이터 포털에서 받은 `CSV` 데이터를 가공해보겠습니다.

- 필요한 데이터 컬럼은 아래와 같습니다.

- 좌측의 숫자는 컬럼 인덱스로 특정 컬럼(카테고리)의 데이터만 가져올 때 필요하여 적어놓았습니다.

- 중간은 카테고리이며 공공데이터 포털에서 지정한 이름입니다.

- 우측은 많은 데이터 중 하나의 예제입니다.

  ```java
  1 상호명| 돈사돈
  4 상권업종대분류명| 음식
  5 상권업종중분류코드| Q01
  6 상권업종중분류명| 한식
  8 상권업종소분류명| 기타고기요리
  10 표준산업분류명| 한식 음식점업
  12 시도명| 제주특별자치도
  14 시군구명| 제주시
  16 행정동명| 노형동
  18 법정동명| 노형동
  24 지번주소| 제주특별자치도 제주시 노형동 3086-3번지 
  31 도로명주소| 제주특별자치도 제주시 우평로 19
  33 신우편번호| 63107
  37 경도| 126.464081358526
  38 위도| 33.4788573479647
  ```



### OpenCsv로 데이터 가공하기 #2

- `CSV` 파일을 쓰는 과정에서 `file size exceeds configured limit code insight` 문제로 파일이 `2.56MB` 이상 작성되지 않는 문제가 발생할 수 있습니다.

- 인텔리제이의 최대 파일 생성 용량이 `2.56MB`로 설정되어있어 발생하는 문제로 아래 설정을 추가하여 변경할 수 있습니다. (설정 후 인텔리제이 재시작)

- 경로 : `Intellij tap` - `Help` - `Edit Custom Properties`

  ```java
  # max file size : default value = 2500 (2.56MB)
  idea.max.intellisense.filesize=40960
  ```

- 구분자가 `','`인 일반적인 `CSV` 양식과는 다르게 서울시 상권 정보 `CSV` 데이터는 `'|'`로 구분되어 있습니다. 아래는 가공 전과 후 데이터입니다.

  - 전

  <img width="1790" alt="csv-before" src="https://user-images.githubusercontent.com/58318041/92559277-37ef9900-f2ab-11ea-946c-cecd986eac54.png">

  - 후

  <img width="1790" alt="csv-after" src="https://user-images.githubusercontent.com/58318041/92559333-5bb2df00-f2ab-11ea-800d-1956f0d940a6.png">

  

### OpenCsv로 데이터 가공하기 #3

- 전체 코드는 아래와 같습니다.

- `CSVReader.readNext()`를 이용해 파일을 한줄 씩 읽어올 수 있습니다. 갖고있는 아이템 갯수만큼 배열이 리턴됩니다.

- 필요한 카테고리를 상수 처리하기 위해 `Enum`을 사용했습니다. (위에서 적어놓은 컬럼 인덱스)

  ```java
  public class OpenCsv {
  
      public static void main(String[] args) throws IOException {
          filterCsvData("./seoul.csv", "./myCsv.csv");
      }
  
      private static void filterCsvData(String path, String newPath) throws IOException {
          CSVReader reader = new CSVReader(new FileReader(path), '|');
          CSVWriter writer = new CSVWriter(new FileWriter(newPath));
  
          String[] nextLine;
          while ((nextLine = reader.readNext()) != null) {
              // 음식 카테고리만 가져온다. 음식 카테고리의 유흥주점은 제외한다.
              if (!nextLine[CATEGORY.number].equals("음식") || nextLine[CATEGORY_MAIN.number].equals("유흥주점")) {
                  continue;
              }
  
              // 기록 순서
              int[] category = {
                  NAME.number,
                  CATEGORY.number,
                  CATEGORY_CODE.number,
                  CATEGORY_MAIN.number,
                  CATEGORY_SUB.number,
                  CATEGORY_INDUSTRY.number,
                  ADDRESS_PROVINCE.number,
                  ADDRESS_CITY.number,
                  ADDRESS_DISTRICT.number,
                  ADDRESS_DISTRICT2.number,
                  ADDRESS_OLD.number,
                  ADDRESS.number,
                  ZIP_CODE.number,
                  LONGITUDE.number,
                  LATITUDE.number
              };
              String[] data = new String[category.length];
              for (int i = 0; i < data.length; i++) {
                  data[i] = nextLine[category[i]];
              }
              writer.writeNext(data);
          }
          writer.close();
      }
  }
  ```

  ```java
  // int[] category = {1, 4, 5, 6, 8, 10, 12, 14, 16, 18, 24, 31, 33, 37, 38};
  public enum Category {
      NAME(1),
      CATEGORY(4),
      CATEGORY_CODE(5),
      CATEGORY_MAIN(6),
      CATEGORY_SUB(8),
      CATEGORY_INDUSTRY(10),
      ADDRESS_PROVINCE(12),
      ADDRESS_CITY(14),
      ADDRESS_DISTRICT(16),
      ADDRESS_DISTRICT2(18),
      ADDRESS_OLD(24),
      ADDRESS(31),
      ZIP_CODE(33),
      LONGITUDE(37),
      LATITUDE(38);
  
      public final int number;
  
      Category(int number) {
          this.number = number;
      }
  }
  ```

  

### 결론

- `OpenCsv`를 이용하여 간편하게 `CSV`파일을 가공할 수 있습니다.
- 가공된 `CSV`를 `DB`에 입력하여 프로젝트에서 다양하게 활용할 수 있습니다.



## References

- [https://codechacha.com/ko/java-write-csv-file-with-opencsv](https://codechacha.com/ko/java-write-csv-file-with-opencsv/)

