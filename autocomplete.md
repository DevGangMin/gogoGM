# autocomplete

  말 그대로 autocomplete(자동완성) 기능을 제공합니다.

 사용자 에이전트의 자동완성을 허용할 양식 입력 필드를 지정할 수 있도록 만들며, 사용자 에이전트에게 어떤 정보에 대한 자동완성을 원하는지 안내할 수도 있습니다.
 
보통 <input>, <select>, <textarea> 태그에 집어넣는 속성입니다만, 여기 지정하지 않은 경우 브라우저는 요소의 양식 소유자, 즉 조상 <form> 요소 또는 form 특성의 값을 id 특성으로 사용하는 <form> 요소의 autocomplete 특성을 사용합니다.

 자동완성으로 제안할 값의 출처는 대개 브라우저가 선택합니다.
 사전 정의된 값을 사용할 수도 있지만, 보통 사용자가 과거에 입력했던 값을 사용합니다.
 예를 들면 사용자가 입력했던 이름, 주소, 전화번호, 이메일 주소등을 브라우저에 저장할 방법을 제공하기도 하고, 신용카드 정보를 미리 암호화해 저장하고, 인증절차 후 자동완성 값으로 쓰게 만들 수 있습니다.

**TIP**
  <input>/<select>/<textarea> 요소가 다음 조건을 만족해야 사용자 에이전트가 자동완성을 제공할 수도 있습니다.
   1. name 또는 id 특성 존재
   2. <form> 요소의 자손일 것
   3. 양식에 <input type="submit"> 버튼이 있을 것

## 값
### off
브라우저가 이 필드에 값을 자동으로 넣는 것을 금지합니다.
문서나 애플리케이션이 자신만의 자동완성 기능을 구현하거나, 보안상 문제로 자동완성을 사용하지 않아야 할 경우 지정할 수 있습니다.

**tip**
    대부분의 최신 브라우저에서는 autocomplete을 off로 지정하더라도 브라우저가 사용자에게 계정 이름과 비밀번호 저장 여부를 묻는 것을 막을 수 없으며, 저장한 값을 사용해 자동완성하는 것도 막을 수 없습니다.

### on
브라우저의 자동완성을 허용합니다. 아무런 안내 정보도 제공하지 않으므로, 브라우저가 스스로의 판단 하에 값을 결정합니다.

### name
사람의 전체 이름. 사람 이름 구조의 복잡함과 다양성으로 인해 보통 개별 이름 구성요소보다 name을 사용하는 것을 선호하지만, 하나씩 받아야 한다면 다음 autocomplete 값도 사용할 수 있습니다.

### honorific-prefix
호칭. "Mrs.", "Mr.", "Miss", "Ms.", "Dr.", "Mlle." 등.

### family-name
성.

### given-name
이름.

### additional-name
가운데 이름.

### honorific-suffix
접미사. "Jr.", "B.Sc.", "PhD.", "MBASW", "IV" 등.

### nickname
별명, 별칭.

### email
이메일 주소.

### username
사용자 또는 계정 이름.

### new-password
새로운 비밀번호. 계정을 생성할 때나 비밀번호를 변경할 때, new-password는 "새로운 비밀번호를 입력하세요" 또는 "비밀번호 확인"에 지정해야 합니다. 일반적인 비밀번호와 구분하는 이유는 브라우저가 기존 비밀번호를 자동완성으로 채우는 것을 피하기 위함이며, 안전한 무작위 비밀번호 생성을 제안할 곳을 결정할 때도 사용하기 때문입니다.

### current-password
사용자의 현재 비밀번호.

### one-time-code
사용자를 인증할 때 사용하는 1회성 코드.

### organization-title
직위. "사장", "매니저", "시니어 테크니컬 커뮤니케이터" 등.

### organization
회사 또는 조직명.

### street-address
도로 주소. 여러 줄의 텍스트도 가능하며 두 번째 행정구역(시/군/구) 내에서 구분할 수 있는 주소여야 하지만, 도시 이름, 우편번호, 국가 이름은 포함해선 안됩니다.

### address-line1, address-line2, address-line3
도로 주소의 각 줄. street-address가 존재하지 않는 경우에만 사용해야 합니다.

### address-level4
4단계 주소에서 가장 상세한 행정구역.

### address-level3
최소 3단계 주소에서의 3단계 행정구역.

### address-level2
최소 2단계 주소에서의 2단계 행정구역. 주소에 사용하는 행정구역을 두 단계로 분류하는 국가에서는 보통 도시, 마을 등입니다.

### address-level1
첫 번째 행정구역. 대한민국에서는 특별시·광역시·도, 미국에서는 주입니다.

### country
국가 코드.

### country-name
국가 이름.

### postal-code
우편번호.

### cc-name
신용카드 등 지불수단 소유자의 전체 이름. 보통 개별 이름 구성요소보다 이 쪽을 더 선호합니다.

### cc-family-name
지불수단 소유자의 성.

### cc-given-name
지불수단 소유자의 이름.

### cc-additional-name
지불수단 소유자의 가운데 이름.

### cc-number
신용카드 번호, 계좌번호 등 지불수단 식별 번호.

### cc-exp
지불수단 유효기간. 보통 MM/YY 또는 MM/YYYY의 형태입니다.

### cc-exp-month
지불수단 유효기간의 월.

### cc-exp-year
지불수단 유효기간의 연도.

### cc-csc
지불수단 보안 코드. 신용카드의 경우 뒷면의 세자리 숫자입니다.

### cc-type
지불수단 유형. "Visa", "Master Card" 등.

### transaction-currency
거래에 사용할 통화 단위.

### transaction-amount
결제 양식에 나타나는 거래량, 금액. 단위는 transaction-currency에 나타난 값을 사용합니다.

### language
선호 언어. 유효한 BCP 47 언어 태그여야 합니다.

### bday
생년월일.

### bday-day
생일.

### bday-month
생월.

### bday-year
생년.

### sex
성별. "남성", "여성", "안드로진" 등 줄바꿈 없는 자유형식 텍스트입니다.

### tel
국가 코드를 포함한 전체 전화번호. 개별 전화번호 구성요소가 필요한 경우 다음 값을 사용할 수 있습니다.

### tel-country-code
국가 코드. 1(북미), 82(대한민국) 등.

### tel-national
국가 코드를 제외한 전체 전화번호. 지역번호도 포함합니다. 대한민국 전화번호 "82-2-555-6502"의 경우 "02-555-6502"가 됩니다.

### tel-area-code
지역번호.

### tel-local
국가 코드와 지역번호를 제외한 전화번호. 두 부분으로 나눌 수도 있으며, "555-6502"의 경우 tel-local-prefix는 "555", tel-local-suffix는 "6502"가 됩나다.

### tel-extension
내선번호.

### impp
인스턴트 메시지 프로토콜 엔드포인트의 URL. "xmpp:username@example.net" 등.

### url
URL. 홈페이지, 회사 웹 사이트 주소 등 양식의 맥락에 맞는 값입니다.

### photo
이미지 URL. 사람, 회사, 연락처 정보 등 양식의 맥락에 맞는 값입니다.