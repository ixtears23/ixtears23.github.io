---
layout: java-basic-post
title: "Gson Sample"
date: 2019-05-09
excerpt: ""
tags: [java,gson]
java-basic: true
comments: true
---



이 포스트에서는 JSON 데이터를 직렬화 및 비 직렬화하는 Gson 및 Jackson API와 Java 객체를 비교할 것입니다.  

Gson과 Jackson은 Java 용 JSON 데이터 바인딩 지원을 제공하는 라이브러리 입니다.  


## Gson Maven Dependency
~~~xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>${gson.version}</version>
</dependency>
~~~

## Gson Serialization


직렬화는 Java 객체를 JSON 출력으로 변환합니다. 다음 엔티티를 고려하십시오.

~~~java
package gsonandjackson;

import java.util.Date;
import java.util.List;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter @Setter
@NoArgsConstructor  @AllArgsConstructor
public class ActorGson {

	private String imdbId;
	private Date dateOfBirth;
	private List<String> filmography;
}
~~~


### Simple Serialization

다음은 JSON 직렬화에 대한 Java의 예 입니다.

~~~java
package gsonandjackson;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Arrays;

import com.google.gson.Gson;

public class GsonTest {

	public static void main(String[] args) throws ParseException {

		SimpleDateFormat dateFormat = new SimpleDateFormat("dd-MM-yyyy");

		ActorGson rudyYoungbldod = new ActorGson(
				"nm2199632",
				dateFormat.parse("21-09-1982"),
				Arrays.asList("Apocalypto", "Beatdown", "Wind Walkers"));

		Movie movie = new Movie(
				"tt0472043",
				"Mel Gibson",
				Arrays.asList(rudyYoungbldod));

		String serializedActor = new Gson().toJson(rudyYoungbldod);
		String serializedMovie = new Gson().toJson(movie);

		System.out.println(serializedActor);
		System.out.println(serializedMovie);
	}
}
~~~

결과 입니다.  

~~~javascript
{"imdbId":"nm2199632","dateOfBirth":"Sep 21, 1982 12:00:00 AM","filmography":["Apocalypto","Beatdown","Wind Walkers"]}
{"imdbId":"tt0472043","director":"Mel Gibson","actors":[{"imdbId":"nm2199632","dateOfBirth":"Sep 21, 1982 12:00:00 AM","filmography":["Apocalypto","Beatdown","Wind Walkers"]}]}
~~~

기본적으로  
 - null 값이 없기 때문에 모든 속성이 직렬화됩니다.  
 - dateOfBirth 필드가 기본 Gson 날짜 패턴으로 변환되었습니다.  
 - 출력 형식이 지정되지 않았으며 JSON 속성 이름이 Java 엔터티에 해당합니다.  


### Custom Serialization

커스텀 시리얼 라이저를 사용하면 표준 동작을 수정할 수 있습니다.  
HTML로 출력 포맷터를 도입하거나, Null 값을 처리하거나, 출력에서 ​​특성을 제외하거나, 새로운 출력을 추가 할 수 있습니다.  

ActorGsonSerializer는 ActorGson 요소에 대한 JSON 코드 생성을 수정합니다.  

~~~java
package gsonandjackson;

import java.lang.reflect.Type;
import java.text.SimpleDateFormat;
import java.util.List;
import java.util.stream.Collectors;

import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonSerializationContext;
import com.google.gson.JsonSerializer;

public class ActorGsonSerializer implements JsonSerializer<ActorGson> {

	private SimpleDateFormat sdf = new SimpleDateFormat("dd-MM-yyyy");

	@Override
	public JsonElement serialize(ActorGson actor, Type typeOfSrc, JsonSerializationContext context) {

		JsonObject actorJsonObj = new JsonObject();

		actorJsonObj.addProperty("<strong>IMDB Code</strong>",	actor.getImdbId());

		actorJsonObj.addProperty("<strong>Date Of Birth</strong>",
				actor.getDateOfBirth() != null ? sdf.format(actor.getDateOfBirth()) : null);

		actorJsonObj.addProperty("<strong>N° Film:</strong>",
				actor.getFilmography() != null ? actor.getFilmography().size() : null);

		actorJsonObj.addProperty("filmography",
				actor.getFilmography() != null ? convertFilmography(actor.getFilmography()) : null);

		return actorJsonObj;
	}

	private String convertFilmography(List<String> filmography) {
		return filmography.stream()
				.collect(Collectors.joining("-"));
	}

}
~~~


director 필드를 제외하기 위해 director 필드에만 `@Expose` 주석을 생략 합니다.  

~~~java
package gsonandjackson;

import java.util.List;

import com.google.gson.annotations.Expose;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter @Setter
@NoArgsConstructor @AllArgsConstructor
public class MovieWithNullValue {

	@Expose
	private String imdbId;
	private String director;

	@Expose
	private List<ActorGson> actors;
}
~~~

이제 GsonBuilder 클래스를 사용하여 Gson 객체 생성을 진행할 수 있습니다.  

~~~java
package gsonandjackson;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Arrays;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

public class ActorGsonTest {

	public static void main(String[] args) throws ParseException {

		Gson gson = new GsonBuilder()
				.setPrettyPrinting()	// 한줄 아닌 줄맞춤 해서 출력
				.excludeFieldsWithoutExposeAnnotation()	// @Expose 주석이 없는 필드는 직렬화에서 제외
				.serializeNulls()	// null도 직렬화
				.disableHtmlEscaping()	// &lt; &gt; 통과
				.registerTypeAdapter(ActorGson.class, new ActorGsonSerializer())
				.create();

		SimpleDateFormat sdf = new SimpleDateFormat("dd-MM-yyyy");

		ActorGson rubyYoungblood = new ActorGson("nm2199632", sdf.parse("21-09-1982"), Arrays.asList("Apocalypto", "Beatdown", "Wind Walkers"));

		MovieWithNullValue movieWithNullValue = new MovieWithNullValue(null, "Mel Gibson", Arrays.asList(rubyYoungblood));

		String serializedMovie = gson.toJson(movieWithNullValue);

		System.out.println(serializedMovie);
	}
}
~~~


결과 입니다.  

~~~javascript
{
  "imdbId": null,
  "actors": [
    {
      "<strong>IMDB Code</strong>": "nm2199632",
      "<strong>Date Of Birth</strong>": "21-09-1982",
      "<strong>N° Film:</strong>": 3,
      "filmography": "Apocalypto-Beatdown-Wind Walkers"
    }
  ]
}
~~~


**요약**  
 - 출력이 포맷 됩니다.  
 - 일부 속성 이름이 변경되어 HTML을 포함합니다.  
 - null 값이 포함되고 디렉터 필드가 생략됩니다.  
 - 날짜는 이제 dd-MM-yyyy 형식으로되어 있습니다.  
 - 새로운 속성 N° Film이 있습니다.  
 - filmography는 기본 JSON 목록이 아닌 형식화 된 속성입니다.  

## Gson Deserialization

### Simple Deserialization

Deserialization은 JSON 입력을 Java 객체로 변환합니다.  
출력을 설명하기 위해 두 엔티티 클래스에서 toString() 메서드를 구현합니다.  

~~~java
package gsonandjackson;

import java.util.List;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

@Getter @Setter
@NoArgsConstructor @AllArgsConstructor
@ToString
public class Movie {

	private String imdbId;
	private String director;
	private List<ActorGson> actors;
}

//-----------------------------------

package gsonandjackson;

import java.util.Date;
import java.util.List;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

@Getter @Setter
@NoArgsConstructor  @AllArgsConstructor
@ToString   // toString()
public class ActorGson {

	private String imdbId;
	private Date dateOfBirth;
	private List<String> filmography;
}
~~~


그런 다음 직렬화 된 JSON을 사용하고 표준 Gson 비 직렬화를 통해 실행합니다.  

~~~java
package gsonandjackson;

import com.google.gson.Gson;

public class DesirializeTest {

	public static void main(String[] args) {

		String jsonInput = "{\"imdbId\":\"tt0472043\",\"actors\":" +
				  "[{\"imdbId\":\"nm2199632\",\"dateOfBirth\":\"1982-09-21T12:00:00+01:00\"," +
				  "\"filmography\":[\"Apocalypto\",\"Beatdown\",\"Wind Walkers\"]}]}";

		Movie outputMovie = new Gson().fromJson(jsonInput, Movie.class);
		String output = outputMovie.toString();
		System.out.println(output);
	}
}
~~~

출력은 JSON 입력의 데이터로 채워진 우리 엔티티입니다.  

~~~javascript
Movie(imdbId=tt0472043, director=null, actors=[ActorGson(imdbId=nm2199632, dateOfBirth=Tue Sep 21 20:00:00 KST 1982, filmography=[Apocalypto, Beatdown, Wind Walkers])])
~~~


### Custom Deserialization

사용자 정의 deserializer를 사용하면 표준 deserializer 의 기능을 수정할 수 있습니다.  
이 경우 dateOfBirth에 대한 올바른 시간대를 날짜에 반영해야 합니다.  
이를 달성하기 위해 ActorGson 엔티티에서 사용자 정의 ActorGsonDeserializer를 사용합니다.  

~~~java
package gsonandjackson;

import java.lang.reflect.Type;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;

import com.google.gson.JsonArray;
import com.google.gson.JsonDeserializationContext;
import com.google.gson.JsonDeserializer;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParseException;

public class ActorGsonDeserializer implements JsonDeserializer<ActorGson> {

	private SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'hh:mm:ss");

	@Override
	public ActorGson deserialize(JsonElement json, Type typeOfT, JsonDeserializationContext context)
			throws JsonParseException {

		JsonObject jsonObject = json.getAsJsonObject();

		JsonElement jsonImdbId = jsonObject.get("imdbId");
		JsonElement jsonDateOfBirth = jsonObject.get("dateOfBirth");
		JsonArray jsonFilmography = jsonObject.getAsJsonArray("filmography");

		ArrayList<String> filmList = new ArrayList<String>();

		if (jsonFilmography != null) {
			for (int i = 0; i < jsonFilmography.size(); i++) {
				filmList.add(jsonFilmography.get(i).getAsString());
			}
		}

		ActorGson actorGson = null;

		try {
			actorGson = new ActorGson(jsonImdbId.getAsString(), sdf.parse(jsonDateOfBirth.getAsString()), filmList);
		} catch (ParseException e) {
			e.printStackTrace();
		}

		return actorGson;
	}
}
~~~

우리는 SimpleDateFormat 파서를 사용하여 시간대를 고려하여 입력 날짜를 파싱했습니다.  

우리는 날짜에 대해서만 사용자 정의 직/병렬 변환기를 작성하기로 결정할 수 있었지만  
ActorGsonDeserializer는 deserialization 프로세스에 대한 보다 자세한 보기를 제공합니다.  

또한 Gson 접근법은 ActorGson 엔티티를 수정할 필요가 없으므로 우리는 항상 입력 엔티티에 액세스하지 못할 수도 있습니다.  
여기서는 사용자 정의 deserializer를 사용합니다.  

~~~java
package gsonandjackson;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

public class CustomDesirializeTest {

	public static void main(String[] args) {
		String jsonInput = "{\"imdbId\":\"tt0472043\",\"actors\":" +
				  "[{\"imdbId\":\"nm2199632\",\"dateOfBirth\":\"1982-09-21T12:00:00+01:00\"," +
				  "\"filmography\":[\"Apocalypto\",\"Beatdown\",\"Wind Walkers\"]}]}";

		Gson gson = new GsonBuilder().registerTypeAdapter(ActorGson.class, new ActorGsonDeserializer()).create();

		Movie outputMovie = gson.fromJson(jsonInput, Movie.class);
		String output = outputMovie.toString();

		System.out.println(output);

	}
}
~~~


날짜가 올바른 표준 시간대를 사용한다는 점을 제외하고는 출력이 단순 deserializer 결과와 유사합니다.  

~~~javascript
Movie(imdbId=tt0472043, director=null, actors=[ActorGson(imdbId=nm2199632, dateOfBirth=Tue Sep 21 00:00:00 KST 1982, filmography=[Apocalypto, Beatdown, Wind Walkers])])
~~~
