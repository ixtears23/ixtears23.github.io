---
layout: spring-test-post
title: "Spring MVC Controller Unit Test"
date: 2019-08-12
tags: [test,spring-test]
excerpt: ""
spring-test: true
comments: true
---

## Spring Framework MVC Controller Unit Test

## SpringRunner 또는 SpringJUnit4ClassRunner 를 사용하지 않은 Controller 단위 테스트
## jsonpath를 사용하지 않고 json 요청과 응답에 대한 테스트

- 사용한 라이브러리
  - Mockito  
  - assertj  
  - junit4  
  - spring-test  

~~~java
package junseok.temp.controller;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import junseok.temp.service.TempService;
import junseok.temp.controller.TempController;

import org.junit.Before;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.mock.web.MockHttpServletResponse;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;


import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;

import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.*;
import static org.assertj.core.api.Assertions.*;


public class TempControllerTest {

    @Mock
    private TempService tempService;

    @InjectMocks
    private TempController tempController;

    private MockMvc mockMvc;

    private final String BASE_URL = "/baseurl";
    private final String APPLICATION_JSON_UTF8 = "application/json;charset=UTF-8";

    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
        this.mockMvc = MockMvcBuilders.standaloneSetup(tempController).build();
    }

    @Test
    public void test_one() throws Exception {

        final String TARGET_URL = "/url.do";
        final String EXPECT_VIEW_NAME = "viewname";

        String url = new StringBuffer(BASE_URL).append(TARGET_URL).toString();


        this.mockMvc.perform(get(url))
                    .andDo(print())
                    .andExpect(status().isOk())
                    .andExpect(view().name(EXPECT_VIEW_NAME));
    }

    @Test
    public void test_two() throws Exception {

        final String REQ_JSON_BODY = "{ \"data\": { \"empNo\": \"00874\", \"yymm\": \"2018\"} }";
        final String RES_JSON_BODY = "{\"errorCode\":\"0\",\"message\":\"\",\"progrmId\":\"\",\"docId\":\"\",\"total\":2,\"errorCalendar\":null,\"data\":[{\"yymm\":\"2018\"},{\"yymm\":\"2019\"}],\"aggregates\":null,\"errorMessage\":\"\"}";

        ResultActions reqResult = this.mockMvc.perform(post(url)
                    .content(REQ_JSON_BODY)
                    .contentType(MediaType.APPLICATION_JSON)
                    .accept(MediaType.APPLICATION_JSON))
                    .andDo(print());

        reqResult.andExpect(content().string(RES_JSON_BODY));
        reqResult.andExpect(status().isOk());
        reqResult.andExpect(content().contentType(APPLICATION_JSON_UTF8));
    }



    @Test
    public void selectSalTax() throws Exception {
        .
        .
        .

        when(TempService.selectSalTax(request.getData())).thenReturn(salTax);

        this.mockMvc.perform(post(TARGET_URL)
                    .contentType(MediaType.APPLICATION_JSON)
                    .accept(MediaType.APPLICATION_JSON)
                    .content(reqBodyJson))
                    .andDo(print())
                    .andExpect(status().isOk())
                    .andExpect(content().contentType(APPLICATION_JSON_UTF8))
                    .andExpect(content().string(resultBodyJson));
    }


    @Test
    public void selectIncentiveTax() throws Exception {

        .
        .
        .

        MockHttpServletResponse response = this.mockMvc.perform(post("/url.json")
                    .contentType(MediaType.APPLICATION_JSON)
                    .accept(MediaType.APPLICATION_JSON)
                    .content(reqBodyString))
                    .andDo(print())
                    .andReturn().getResponse();

        String contentType = response.getContentType();
        int status = response.getStatus();
        String bodyAsString = response.getContentAsString();

        DataSourceResult dataSourceResult = new DataSourceResult();
        dataSourceResult.setData(makeIncenTax);
        String resBodyString = objectMapper.writeValueAsString(dataSourceResult);

        assertThat(contentType).isEqualTo(APPLICATION_JSON_UTF8);
        assertThat(status).isEqualTo(HttpStatus.OK.value());
        assertThat(bodyAsString).isEqualTo(resBodyString);
    }


}

~~~

> 중요한 부분만 제외하고 거의 대부분 생략  
> 완성된 코드에서 이름만 조금씩 바꾸고 생략해서 실제 컴파일 오류는 확인하지 않음.  


## 알아두어야 하는 키워드

 - `@Mock`  
 - `@InjectMocks`  
 - json 문자열 작성 방법
 - `objectMapper.writeValueAsString`
 - `MockMvcBuilders.standaloneSetup`
 - `MockitoAnnotations.initMocks(this)`


### `MockMvcBuilders.standaloneSetup`
스프링 구성을로드하지 않고 컨트롤러 인스턴스를 수동으로 생성하는 것입니다.  
대신 MVC JavaConfig 또는 MVC 네임 스페이스와 비슷한 기본 기본 구성이 자동으로 생성됩니다.  
어느 정도 사용자 정의 할 수 있습니다.  

### `objectMapper.writeValueAsString`  
Java 값을 문자열로 직렬화하는 데 사용할 수있는 메소드입니다.  
`StringWriter`로 `writeValue(Writer, Object)`를 호출하고 `String`을 구성하는 것과 기능상 동일하지만보다 효율적입니다.  

### `MockitoAnnotations.initMocks`
Mockito 관련 annotation을 사용하기 위해 필요합니다.  
`@RunWith(MockitoJUnitRunner)` 를 대신 사용할 수도 있습니다.  
