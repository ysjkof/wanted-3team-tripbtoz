# wanted-3team-tripbtoz

## 커스텀 훅 문서(데이터 불러오기)

### useHotels

**호텔 목록**와 관련된 기능

- 훅을 실행하면 { isLoading, hotels, userHotels, hotelInfo, getResultsByPage, getHotelInfo } 6개 값을 가진 객체를 반환합니다.

- import할 때 전체 데이터중 첫번째 페이지의 데이터(10개)를 초기값으로 불러옵니다.

- loading은 훅이 데이터를 불러오는 동안 boolean으로 반환한합니다.

- hotels는 현재 페이지까지 불러온 호텔목록 입니다.

- userHotels는 예약한(로컬스토리지에 저장된) 호텔목록입니다.

- hotelInfo는 호텔상세정보입니다.

- getResultsByPage에 `(searchParameter: UserDataType | null, page: number = 1)` 를 주고 실행하면 검색조건에 맞는 새로운데이터를 페이지당 10개씩 요청합니다.

- getHotelInfo에 `(hotelName: string)`을 주고 실행하면 호텔상세정보를 요청합니다.

- 페이지 요청시 대기시간은 500ms 입니다.

- 검색 결과가 아닌 전체데이터를 요청하려면 getResultsByPage 함수의 `searchParameter` 인자에 null을 주고 실행합니다.

```ts
interface UserDataType {
  hotelName: string;
  checkInDate: Date;
  checkOutDate: Date;
  numberOfGuests: number;
}
```

## 예약 페이지 개발 요구사항

- [x] 체크 인/아웃 날짜를 선택할 수 있는 캘린더 구현
- [x] 투숙객 수를 입력할 수 있는 인풋 폼 구현
- [ ] 제공되는 hotels.json 파일의 데이터 중 체크 인/아웃 기간과 인원수에 해당하는 호텔들을 조회
- [ ] 조회 된 호텔을 무한 스크롤로 노출
- [ ] 호텔 하나를 선택하게 되면 선택한 (체크 인/아웃 - 투숙객 수 - 호텔명) 정보를 가지는 데이터를 로컬 스토리지에 json타입으로 저장.

### 제약 사항

- [x] 반응형 디자인을 기본으로 PC / MOBILE 두가지 형태로 모바일은 넓이 480px를 가진다.
- [ ] 반응형 레이아웃은 캘린더 - 투숙객 선택 - 호텔 목록 순 - 캘린더

#### 캘린더

- [ ] 오늘 날짜부터 12개월까지 보여지는 캘린더를 반응형으로 구현
- [x] 이번달의 지난날은 모두 선택되지 않아야 함
- [x] 처음 캘린더를 선택할 때 default로 일주일 뒤 1박으로 체크인 되도록 함
- [x] 처음 선택한 날이 체크인이 되며 두 번째 선택한 날이 체크아웃으로 설정됨
- [x] 처음 선택한 날보다 이전 날짜를 선택한 경우 처음 선택한 날짜가 초기화 되고 두 번째 선택한 날짜가 체크인으로 됨
- [x] 시작일(체크인)과 종료일(체크아웃) 날짜는 highlighting 되며 체크인과 체크아웃 사이의 날짜도 동일하게 highlighting 됨
- [x] 시작일(체크인)과 종료일(체크아웃)을 선택한 상태에서 다른 날짜를 선택하면 기존 선택값은 초기화 되며, 선택한 다른 날짜가 시작일(체크인)이 됨

#### 투숙객 인풋 폼

- [x] 성인 / 아동 구분이 되어야 함.
- [x] 성인의 기본값은 2
- [x] 아동의 기본값은 0
- [x] 투숙객 수 는 - 와 + 버튼을 통해 조절

#### 호텔 조회

- [ ] 조회 버튼 구현 후 해당 버튼의 이벤트로 호텔을 조회 할 수 있어야 한다.
- [ ] hotels.json 파일 탐색
- [ ] 예약 가능한 호텔의 수량은 모두 하루에 1개씩 존재 함을 가정

구조설명

```json
{
  "hotel_name": "테스트 호텔", // 호텔명
  "occupancy": {
    "base": 2, // 기준 투숙 인원
    "max": 4 // 최대 투숙 인원
  }
}
```

#### 조회 목록

- [ ] 조회된 호텔 목록을 무한 스크롤이 가능한 구조로 구현 하여야 한다.
- [ ] 한 페이지의 한번의 최대 노출 갯수는 10개 이다.
- [ ] 다음 페이지 데이터 로딩중에는 로딩바가 노출되어야 한다. (현재는 mock 데이터 기준이기 때문에 무조건 500ms의 대기시간을 가지도록 한다.)

#### 저장

- [ ] 예약 버튼을 구현 하고 해당 버튼의 이벤트로 저장을 수행한다.
- [ ] localstorage에 호텔명을 key로 하는 json value를 저장한다.
