# Test Code의 작성
> 어떤 기능을 구현하는 코드를 작성하고, 해당 코드가 잘 작동하는 지를 확인할 때 Test Code를 사용함

### 예제 코드 작성법
Test Code 대신에 구현한 기능을 사용하는 예제를 간단하게 만들어서 print해보면 해당 기능이 잘 작동하는 지를 테스트 할 수 있음

``` java
public class MemberApp {
    public static void main(String[] args) {
        MemberService memberService = new MemberServiceImpl();
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("findMember = " + findMember.getName());
    }
}
```

<mark>
<문제점> </br>
눈으로 print 결과물을 직접 확인해야 하다보니 실수가 발생할 수 있고, 복잡한 코드는 예시 코드를 작성하는 것도 힘들 수 있음. 또한 print 된 결과물만으로는 비교가 어려운 Test를 해야하는 때도 발생
</mark>

### Test Code 작성법
Test Code를 작성하여 print해서 눈으로 비교해보는 방식이 아니라 컴퓨터가 비교한 결과를 바탕으로 구현한 코드를 test 해볼 수 있음

``` java
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;

public class MemberServiceTest {
    
    MemberService memberService = new MemberServiceImpl();

    @Test
    void join() {
        //given
        Member member = new Member(1L, "memberA", Grade.VIP);

        //when
        memberService.join(member);
        Member findMember = memberService.findMember(1L);

        //then
        Assertions.assertThat(member).isEqualTo(findMember);
    }

}
```

- *given* : 무엇이 주어진 경우
- *when* : 어떤 메서드가 호출될 때
- *then* : 비교를 통해 제대로 작동하는 지 test 해보아라