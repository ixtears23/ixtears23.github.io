---
layout: java-solid-post
title: "개방 폐쇄 원칙(Open Closded principle)"
date: 2019-03-18
excerpt: ""
tags: [java,solid,ocp]
java-solid: true
comments: true
---


## 소프트웨어 개체 (클래스, 모듈, 함수 등)는 확장을 위해 열려 있어야하지만 수정을 위해 닫혀 있어야 합니다.

이 원칙의 목표는 소스 코드를 수정하지 않고 모듈의 동작을 확장하는 것입니다.
우리 제품 중 하나에 할인을 적용하는 시나리오를 상상해보십시오.
할인 서비스는 지정된 할인을 적용하고 할인 된 가격을 돌려 줄 것입니다.
현재 우리 시스템에는 모든 성인에게 적용되는 단 한 종류의 할인 혜택 만 있습니다.

~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;
import java.math.RoundingMode;

public class Discount {

	public BigDecimal apply(BigDecimal price) {
		BigDecimal percent = new BigDecimal("0.10");
		BigDecimal discount = price.multiply(percent);
		return price.subtract(discount).setScale(2, RoundingMode.HALF_UP);
	}

}
~~~

그리고 할인 서비스는 주어진 가격에 이 할인을 적용해야합니다.

~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;

public class DiscountService {

	public BigDecimal applyDiscounts(BigDecimal price, Discount discount) {
		return discount.apply(price);
	}
}
~~~

그러나 우리 회사는 연장자에게 할인 혜택을 주고자 하므로, 별도의 연장자 할인을 받습니다.  

~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;
import java.math.RoundingMode;

public class SeniorDiscount {

	public BigDecimal apply(BigDecimal price) {
		BigDecimal percent = new BigDecimal("0.20");
		BigDecimal discount = price.multiply(percent);
		return price.subtract(discount).setScale(2, RoundingMode.HALF_UP);
	}
}
~~~

이것은 서비스가 성인을위한 할인과 연장자를위한 할인 모두를 적용해야하기 때문에 할인 서비스에 대해 좀 더 복잡하게 만듭니다.
~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;

public class DiscountService {

	public BigDecimal applyDiscounts(BigDecimal price, Discount discount) {

		BigDecimal discountPrice = price.add(BigDecimal.ZERO);
		discountPrice = discount.apply(discountPrice);

		return discountPrice;
	}

	public BigDecimal applySeniorDiscount(BigDecimal price, SeniorDiscount discount) {
		return discount.apply(price);
	}
}
~~~

그렇게함으로써 할인 서비스 소스 코드를 수정하여 서비스를 확장했습니다.
또한 영업 부서가 제시 다른 할인에 제시한다면 할인 서비스는 추가 될 수 있습니다.


개방/폐쇄 원칙을 따르기 위해 우리는 할인 인터페이스를 만들 것입니다.  
~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;

public interface Discount {

	BigDecimal apply(BigDecimal price);
}
~~~

기본 할인은 AdultDiscount로 이름이 바뀌고 할인 인터페이스를 구현합니다.
~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;
import java.math.RoundingMode;

public class AdultDiscount implements Discount {

	@Override
	public BigDecimal apply(BigDecimal price) {

		BigDecimal percent = new BigDecimal("0.10");
		BigDecimal discount = price.multiply(percent);

		return price.subtract(discount.setScale(2, RoundingMode.HALF_UP));
	}

}
~~~

연장자 할인은 위한 SeniorDiscount클래스를 만들어서 Discount 인터페이스를 구현합니다.
~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;
import java.math.RoundingMode;

public class SeniorDiscount implements Discount {

	@Override
	public BigDecimal apply(BigDecimal price) {
		BigDecimal percent = new BigDecimal("0.20");
		BigDecimal discount = price.multiply(percent);
		return price.subtract(discount.setScale(2, RoundingMode.HALF_UP));
	}
}
~~~


마지막으로 Discount 인터페이스를 기반으로 할인을 적용하려면 DiscountService를 리팩토링해야합니다.  
~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;
import java.util.Arrays;
import java.util.List;

public class DiscountService {

	public BigDecimal applyDiscounts(BigDecimal price, Discount[] discounts) {

		BigDecimal discountPrice = price.add(BigDecimal.ZERO);

		List<Discount> discountSet = Arrays.asList(discounts);
		discountSet.forEach(d -> d.apply(discountPrice));

		return discountPrice;
	}

}
~~~

이렇게 하면 할인 서비스는 소스 코드를 변경하지 않고 다른 할인을 적용 할 수 있습니다.

추후에 기본 할인 적용이라는 할인이 추가되었을 경우 아래와 같이 BasicDiscount 클래스를 추가하면 됩니다.  

~~~java
package com.solid.ocp.sample01;

import java.math.BigDecimal;
import java.math.RoundingMode;

public class BasicDiscount implements Discount {

	@Override
	public BigDecimal apply(BigDecimal price) {
		BigDecimal percent = new BigDecimal("0.01");
		BigDecimal discount = price.multiply(percent);
		return price.subtract(discount.setScale(2, RoundingMode.HALF_UP));
	}
}
~~~

### 개방폐쇄 원칙을 따름으로써 기존 소스코드를 수정하지 않고도 기능을 확장할 수 있습니다.

[출처](https://egkatzioura.com/2018/02/23/solid-principles-open-closed-principle/)
