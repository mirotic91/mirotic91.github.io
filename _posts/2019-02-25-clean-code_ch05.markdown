---
layout: post
title: "Ch05. 형식 맞추기"
subtitle: 형식을 깔끔하게 맞춰 코드를 짜자
date: 2019-02-25 22:45:13 +0900
categories: book clean-code
---

# 형식 맞추기
---
- 프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다.
- 팀이 합의하여 **규칙**을 정하고 모두가 그 규칙을 따라야 한다.

#### 형식을 맞추는 목적
- 코드 형식은 **의사소통**의 일환이다. 의사소통은 전문 개발자의 일차적인 의무다.
- 구현한 코드들이 변경되더라도 개발자의 스타일과 규율은 잘 사라지지 않는다.

#### 적절한 행 길이를 유지하라
- 자바에서 파일 크기는 클래스 크기와 밀접하다.
- 일반적으로 작은 파일이 큰 파일보다 이해하기 쉽다.

##### 신문 기사처럼 작성하라
- 독자는 위에서 아래로 읽는다.
- 메소드 전체 내용을 요약하는 이름을 작성한다.
- 세세한 내용은 또 하나의 메소드로 분리한다.

##### 개념은 빈 행으로 분리하라
- 빈 행은 새로운 개념을 시작한다는 시각적 단서다.
- 다른 개념 사이에 `개행`이 빠지면 가독성이 현저히 떨어진다.

##### 세로 밀집도
- 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다.

##### 수직 거리
- 같은 파일에 속할 정도로 밀접한 두 개념은 세로 거리로 연관성을 표현한다.
- 여기서 뜻하는 `연관성`이란 한 개념을 이해하는데 다른 개념이 중요한 정도다.
- 연관성이 깊은 두 개념이 멀리 떨어져 있으면 코드를 읽는 사람이 함수 연관관계와 동작 방식을 이해하기 위해 소스 파일 내 여기저기 찾느라 시간과 노력을 소모한다.

###### 변수 선언
- `변수`는 처음으로 사용하기 직전에 선언하며 수직으로 가까운 곳에 위치해야 한다.
- `비공개 함수`는 처음으로 호출한 직후에 정의해야 쉽게 눈에 띄어야 한다.

###### 인스턴스 변수
- `인스턴스 변수`는 클래스 맨 처음에 선언한다.

###### 종속함수
- 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.
- 또한 가능하다면 `호출하는 함수`를 호출되는 함수보다 먼저 배치해야 자연스럽게 읽힌다.
  ```java
  public void checkAdmin() {
    if (!isAdmin()) {
      throw new BusinessException();
    }
  }
  
  private boolean isAdmin() {
    return Role.ADMIN == this.roleName;
  }
  ```
  
###### 개념적 유사성
- 개념적인 친화도가 높을수록 코드를 가까이 배치한다.
- 종속적인 관계가 없더라도 기능적으로 유사하다.  
  ```java
  private boolean isAdmin() {
    return Role.ADMIN == this.roleName;
  }

  private boolean isManager() {
    return Role.MANAGER == this.roleName;
  }
  ```

##### 세로 순서
- **호출되는 순서대로 함수를 배치**하면 소스 코드 모듈이 고차원에서 저차원으로 자연스럽게 내려간다.
- 가장 중요한 개념을 표현하고나서 세세한 사항을 표현하면 함수 몇 개만 읽어도 개념을 파악하기 쉬워져서 세세한 사항까지 파고들 필요가 없다.

#### 가로 형식 맞추기
- 행 길이는 어느 정도가 적당할까?  
여러 논쟁이 있지만 120자 정도로 제한하는 것을 권고한다고 한다.

##### 가로 공백과 밀집도
- 가로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다.
  - ex) 메소드 파라미터 구분, 할당 연산자, 연산자 우선순위 강조
  
##### 가로 정렬
- 정렬을 통해 강조하려고한 진짜 의도가 가려질 수 있다.  
변수 유형은 무시하고 변수 이름부터 읽게 된다.
- 정렬이 필요할 정도로 목록이 길다면 문제는 목록 길이지 정렬 부족이 아니다.  
만약 선언부가 길어진다면 클래스를 쪼개야 한다는 의미다.

##### 들여쓰기
- 들여쓰기가 없다면 인간이 코드를 읽기란 거의 **불가능**일 것이다.
- 들여쓰기한 파일은 구조가 한눈에 들어온다.
  - ex) 변수, 메소드, 분기문 등
  
*들여쓰기 무시하기*
- 간혹 간단하고 짧은 메소드에서 규칙을 무시하고픈 유혹이 생긴다.
```java
# before
  public String render() { return ""; }
# after    
  public String render() {
    return "";
  }
```

##### 가짜 범위
- 때로는 빈 while 문이나 for 문을 접한다.
빈 블록을 올바로 들여쓰고 괄호로 감싸야 한다. 그렇게 하지 않으면 눈에 띄지 않는다.

#### 팀 규칙
- 각자 선호하는 규칙이 있지만 `팀`에 속한다면 자신이 선호해야할 규칙은 바로 **팀 규칙**이다. 그래야 스타일이 일관적이고 매끄럽다.
- 팀 규칙을 정하여 IDE 코드 형식기를 설정하여 모두가 그 규칙을 따라야 한다.
- 좋은 소프트웨어 시스템은 **읽기 쉬운** 문서로 이뤄진다.

#### 밥 아저씨의 형식 규칙
- 코드 자체가 최고의 구현 표준 문서가 되는 예

```java
public class CodeAnalyzer implements JavaFileAnalysis { 
    
    private int lineCount;
	private int maxLineWidth;
	private int widestLineNumber;
	private LineWidthHistogram lineWidthHistogram; 
	private int totalChars;
	
	public CodeAnalyzer() {
		lineWidthHistogram = new LineWidthHistogram();
	}
	
	public static List<File> findJavaFiles(File parentDirectory) { 
		List<File> files = new ArrayList<File>(); 
		findJavaFiles(parentDirectory, files);
		return files;
	}
	
	private static void findJavaFiles(File parentDirectory, List<File> files) {
		for (File file : parentDirectory.listFiles()) {
			if (file.getName().endsWith(".java")) 
				files.add(file);
			else if (file.isDirectory()) 
				findJavaFiles(file, files);
		} 
	}
	
	public void analyzeFile(File javaFile) throws Exception { 
		BufferedReader br = new BufferedReader(new FileReader(javaFile)); 
		String line;
		while ((line = br.readLine()) != null)
			measureLine(line); 
	}
	
	private void measureLine(String line) { 
		lineCount++;
		int lineSize = line.length();
		totalChars += lineSize; 
		lineWidthHistogram.addLine(lineSize, lineCount);
		recordWidestLine(lineSize);
	}
	
	private void recordWidestLine(int lineSize) { 
		if (lineSize > maxLineWidth) {
			maxLineWidth = lineSize;
			widestLineNumber = lineCount; 
		}
	}

	public int getLineCount() { 
		return lineCount;
	}

	public int getMaxLineWidth() { 
		return maxLineWidth;
	}

	public int getWidestLineNumber() { 
		return widestLineNumber;
	}

	public LineWidthHistogram getLineWidthHistogram() {
		return lineWidthHistogram;
	}
	
	public double getMeanLineWidth() { 
		return (double)totalChars/lineCount;
	}

	public int getMedianLineWidth() {
		Integer[] sortedWidths = getSortedWidths(); 
		int cumulativeLineCount = 0;
		for (int width : sortedWidths) {
			cumulativeLineCount += lineCountForWidth(width); 
			if (cumulativeLineCount > lineCount/2)
				return width;
		}
		throw new Error("Cannot get here"); 
	}
	
	private int lineCountForWidth(int width) {
		return lineWidthHistogram.getLinesforWidth(width).size();
	}
	
	private Integer[] getSortedWidths() {
		Set<Integer> widths = lineWidthHistogram.getWidths(); 
		Integer[] sortedWidths = (widths.toArray(new Integer[0])); 
		Arrays.sort(sortedWidths);
		return sortedWidths;
	} 
}
```

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
