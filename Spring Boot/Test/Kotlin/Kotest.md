## Kotest
- JUnit과 비슷한 테스트 도구
- Kotlin DSL을 사용해 더 코틀린 스러운 테스트 작성 가능

### mock
- 객체를 외부 의존성에 대해서 통제가능한 선으로 만드는 것
- 실제 외부객체가 아닌 dummy 객체를 호출하도록 한다.

### SpringSpec
- @Test 어노테이션이 필요 없음
- 테스트 명을 fun 키워드 없이 **String**으로 작명 가능
```
class UserTest: StringSpec({
    "회원의 비밀번호와 일치하는지 확인한다" {
        val user = createUser()
        shouldNotThrowAny { user.authenticate(PASSWORD)}
    }
})
```

### BehaviorSpec
- Given-When-Then 테스트 패턴을 쓰고싶을 때 사용한다.
```
internal class UserServiceTest : BehaviorSpec({
    val userRepository: UserRepository = mockk()
    val passwordGenerator: PasswordGenerator = mockk()
    Given("유저의 비밀번호가 주어질 때") {
        When("비밀번호를 변경하려 하면") {
            var request: EditPasswordRequest = mockk()
            slot<Long>().also { slot ->
                every {
                    userRepository.getById(cpature(slot))
                } answers {
                    createUser(id = slot.captured)
                }
                Then("확인용 비밀번호가 일치한다면 변경한다.") {

                }
            }
        }
    }
})
```

### DescribeSpec
- DCI 패턴을 사용하고 싶을 때 사용한다.
    - 테스트 중첩이 가능하다는 장점 존재
```
internal class UserServiceTest: DescribeSpec({
    val userRepository: UserRepository = mockk()

    describe("UserService") {
        var user: User = createUser()
        val request: ResetPasswordRequest = mockk()

        context("비밀번호를 비교할 때") {
            var request: EditPasswordRequest = mockk()
            slot<Long>().also { 
                slot -> every {
                    userRepository.getById(capture(slot))
                } answers {
                    createUser(id = slot.captured)
                }
            }
            it("확인용 비밀번호가 일치한다면 변경한다.") {

            }
        }
    }
})
```

### FunSpec
- 함수형태로 테스트 코드를 작성할 수 있도록 도와준다.
```
class FunSpecTests: FunSpec({
    test("String length should be return the length of the string") 
    {
        "sammy".length shouldBe 5
        "".length shouldBe 0
    }
})
```