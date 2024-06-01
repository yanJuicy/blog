## 입출력 스트림

자바는 다양한 입출력 장치에 독립적으로 일관성있는 입출력을 입출력 스트림을 통해 제공한다

- 파일 디스크, 키보드, 마우스, 네트워크, 메모리 등 모든 자료가 입력되고 출력되는 곳

![](https://gitlab.com/easyspubjava/javacoursework/-/raw/master/Chapter6/6-12/img/io.png)

## 입출력 스트림의 구분

- 대상 기준: 입력 스트림 or 출력 스트림
- 자료의 종류: 바이트 스트림 or 문자 스트림
- 기능: 기반 스트림 or 보조 스트림

## 입력 스트림과 출력 스트림

- 입력 스트림: 대상으로부터 자료를 읽어 들이는 스트림이다
- 출력 스트림: 대상으로 자료를 출력하는 스트림이다

![](https://gitlab.com/easyspubjava/javacoursework/-/raw/master/Chapter6/6-12/img/iostream.png)

### 스트림의 종류

| 종류     | 예시                                                                   |
|--------|----------------------------------------------------------------------|
| 입력 스트림 | FileInputStream, FileReader, BufferedInputStream, BufferedReader 등   |
| 출력 스트림 | FileOutputStream, FileWriter, BufferedOutputStream, BufferedWriter 등 |


## 바이트 단위 스트림과 문자 단위 스트림

- 바이트 단위 스트림: 동영상, 음악 파일, 실행 파일등의 자료를 읽고 쓸 때 사용한다
- 문자 단위 스트림: 바이트 단위로 자료를 처리하면 문자는 깨진다, 인코딩에 맞게 2바이트 이상으로 처리하도록 구현됐다

![](https://gitlab.com/easyspubjava/javacoursework/-/raw/master/Chapter6/6-12/img/byte.png)


### 스트림의 종류

| 종류      | 예시                                                                            |
|---------|-------------------------------------------------------------------------------|
| 바이트 스트림 | FileInputStream, FileOutputStream, BufferedInputStream, BufferedOutputStream 등 |
| 문자 스트림  | FileReader, FileWriter, BufferedReader, BufferedWriter 등                      |


## 기반 스트림과 보조 스트림

- 기반 스트림: 대상에 직접 자료를 읽고 쓰는 기능의 스트림
- 보조 스트림: 직접 읽고 쓰는 기능은 없이 추가적인 기능을 더해주는 스트림이다

보조 스트림은 직접 읽고 쓰는 기능은 없으므로 항상 기반 스트림이나 또 다른 보조 스트림을 생성자의 매개변수로 포함한다

![](https://gitlab.com/easyspubjava/javacoursework/-/raw/master/Chapter6/6-12/img/second.png)

### 스트림의 종류

| 종류     | 예시                                                                                 |
|--------|------------------------------------------------------------------------------------|
| 기반 스트림 | FileInputStream, FileOutputStream, FileReader, FileWriter 등                        |
| 보조 스트림 | InputStreamReader, OutputStreamWriter, BufferedInputStream, BufferedOutputStream 등 |


