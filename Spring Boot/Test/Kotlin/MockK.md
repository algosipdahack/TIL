## MockK
- 코틀린을 위한 Mock 프레임워크
- 자바에서 사용하는 Mockito와 유사

### 1. 모의 객체 생성
```
1) mockk<타입>()
private val mockValidator1 = mockk<CreationValidator>()

2) 타입 추론 -> 변수나 프로퍼티 타입이 명시적으로 정의되어 있으면 생략 가능
private val mockValidator2 : CreationValidator = mockk()
```

### 2. Answer 정의  / 응답값 설정(stub)
```
@Test
fun someMockTest() {
    every { mock.someMethod(1) } returns "OK"
    every { mock.someMethod(2) } throws SomeException()
    every { mock.call() } just Runs

    assertEquals("OK", mock.someMethod(1))
    assertThrows<SomeException> { mock.someMethod(2) }
}
```
- 임의의 인자와 일치하도록 설정하려면 any()를 사용
- 리턴 타입이 Unit인 함수일 때 relaxUnitFun파라미터 값을 true로 전달한다.
```
val mock = mockk<Some>(relaxUnitFun = true)
mock.someMethod(1) --> 0을 리턴한다.
```
- relax 파라미터 값을 true로 전달하면 호출대상에 대한 stub 정의가 없어도 된다.


### 3. 호출여부 검증
- verify함수 사용
- 메서드가 테스트 안에서 정상적으로 호출되었는지를 검증
```
@Test
fun test() {
    val mappingRepository: MappingRepository = mockk()

    every { mappingRepository.count() } returns 100

    verify(exactly = 1) { mappingRepository.count() }
}
```
- 한번만 호출된다는 것을 검증하는 코드

### 4. 인자 캡처
- capture()을 통해서 slot에 값 저장 가능 > 이를 slot.captured를 통해서 가져올 수 있다.
```
@Test
fun test() {
    val mappingRepository: MappingRepository = mockk()
    val slot: CapturingSlot<Mapping> = slot() // capture 준비

    val mapping = Mapping(1L, UserType.A, 2L, UserType.U, 3L, false)

    every { mappingRepository.save(mapping) } returns mapping

    mappingRepository.save(mapping)

    verify(exactly = 1) { mappingRepository.save(capture(slot)) } // save의 파라미터를 캡쳐
    val captured = slot.captured // 캡쳐된 파라미터를 가져오기

    Assertions.assertEquals(captured.id, 1L) // 내용 확인
}
```

### 5. private function mocking
- dynamic call 사용
- 호출 객체를 spyk로 spy 객체로 만들어야 한다.
  - 실제 객체의 코드를 사용하지만 **stub를 한** 메서드에 한해서만 내부 로직을 실행하지 않고 정해진 응답을 반환할 수 있도록 하는 테스트 방법
```
@Test
fun test() {
    val mappingRepository: MappingRepository = mockk()
    val mappingService: MappingService = spyk(
        objToCopy = MappingService(mappingRepository),
        recordPrivateCalls = true
    )

    every { mappingService["validate"](2L) } returns Unit // private function mock & stub 
}
```
- mappingService["validate"](2L) 이런 형식으로 가능



## Reference
---
https://javacan.tistory.com/entry/kotlin-mock-framework-mockk-intro
https://sabarada.tistory.com/191