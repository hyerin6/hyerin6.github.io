---
layout: post    
title: "프로젝트에서 Enum 사용해보기"      
description: "Enum Converter "      
date: 2021-08-06    
tags: [spring, jpa, enum, java]     
categories: [Java, Spring]      
comments: true     
share: true  
---   

<br />     

지금까지 프로젝트에서 Enum을 사용할 때, `남성/여성`을 표현하기 위해 사용해본 적이 있다.   
JPA 프로젝트에서 enum을 사용하는 방법은 다음과 같다.     

<br />    

## `@Enumerated`       
`@Enumerated` 은 두가지 저장 방법을 제공한다.   

* `EnumType.ORDINAL`: ENUM이 정의된 순서의 인덱스로 DB에 값을 저장         
  Enum에 정의된 순서를 기반으로 인덱스가 만들어지고, DB에 저장되는 방식인데      
  값이 추가될 때 순서가 바뀌면서 데이터를 잘못 가져오는 문제가 발생할 수 있다.             
<br />          


* `EnumType.STRING`: ENUM의 이름 자체를 DB에 값으로 저장.      
  Name 값을 그대로 DB에 저장하는 방식인데, Name 값을 변경해야 하는 상황이라면 바뀐 데이터를 처리하는 일이 생기고        
  DB에 데이터를 낭비하면서 넣게 된다.      
  

<br />       
<br />      


## `@Converter`    
DB에 값을 효율적이게 저장할 수 있는 방법이 바로 `@Converter` 이다.   

> @Convertor    
> 아래와 같은 구조로 사용된다.      
> `영속성 컨텍스트 > Convetor > DB`         
> 영속성 컨텍스트에 데이터가 들어가고, 실제 디비로 들어가거나 나오기 직전에     
> Convetor 로직이 있다면 돌고 난 후에 DB로 접근하게 되어있다.     

 
<br />    


프로젝트에 적용한 부분은 다음과 같다.  
좋아요(Like) 기능을 게시글(Post)과 댓글에(Comment) 적용하기 위해     
Type enum을 만들고 `@Enumerated` 사용의 문제점을 해결하기 위해    
`AttributeConverter`를 구현했다.      

<br />     

* Like  

```
@Entity
public class Like {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@Convert(converter = LikeTypeConverter.class)
	private Type type;

	@ManyToOne
	@JoinColumn(name = "user_id")
	private User user;

	private Long parentId;

	. . .

}
```

<br />    

* Type   

```
public enum Type {
	POST(1), COMMENT(2);

	public int type;

	Type(int type) {
		this.type = type;
	}

	public int toDbValue() {
		return type;
	}

	public static Type from(Integer dbData) {
		return Stream.of(Type.values())
			.filter(x -> x.type == dbData)
			.findFirst()
			.orElseThrow(IllegalArgumentException::new);
	}

}
```

<br />    


* LikeTypeConverter         

```
@Converter
public class LikeTypeConverter implements AttributeConverter<Type, Integer> {

    // DB에 어떤 값이 저장되는지 
	@Override
	public Integer convertToDatabaseColumn(Type attribute) {
		return attribute.toDbValue();
	}
	
    // DB에서 Entity로 값을 넣을 때 어떤 값을 리턴하는지 
	@Override
	public Type convertToEntityAttribute(Integer dbData) {
		return Type.from(dbData);
	}
}
```

<br />    


## 참고       
* <https://techblog.woowahan.com/2600/>   
* <https://tech.yangs.kr/16>    
* <https://hungrydiver.co.kr/bbs/detail/develop?id=43>    

