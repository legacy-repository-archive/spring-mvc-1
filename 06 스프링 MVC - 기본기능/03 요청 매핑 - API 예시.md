요청 매핑 - API 예시
========
**회원 관리 API**  
* 회원 목록 조회: `GET /users`
* 회원 등록: `POST /users`
* 회원 조회: `GET /users/{userId}`
* 회원 수정: `PATCH /users/{userId}`
* 회원 삭제: `DELETE /users/{userId}`   
  
```java
@RestController
@RequestMapping("/mapping/users")
public class MappingClassController {
     /**
     * GET /mapping/users
     */
     @GetMapping
     public String users() {
         return "get users";
     }
     /**
     * POST /mapping/users
     */
     @PostMapping
     public String addUser() {
         return "post user";
     }
     /**
     * GET /mapping/users/{userId}
     */
     @GetMapping("/{userId}")
     public String findUser(@PathVariable String userId) {
         return "get userId=" + userId;
     }
     /**
     * PATCH /mapping/users/{userId}
     */
     @PatchMapping("/{userId}")
     public String updateUser(@PathVariable String userId) {
         return "update userId=" + userId;
     }
     /**
     * DELETE /mapping/users/{userId}
     */
     @DeleteMapping("/{userId}")
     public String deleteUser(@PathVariable String userId) {
        return "delete userId=" + userId;
    }
}
```

**@RequestMapping("/mapping/users")**    
클래스 레벨에 매핑 정보를 두면 메서드 레벨에서 해당 정보를 조합해서 사용한다.     
   
* 회원 목록 조회: `GET /mapping/users`
* 회원 등록: `POST /mapping/users`
* 회원 조회: `GET /mapping/users/id1`
* 회원 수정: `PATCH /mapping/users/id1`
* 회원 삭제: `DELETE /mapping/users/id1`
   
매핑 방법을 이해했으니, 이제부터 HTTP 요청이 보내는 데이터들을 스프링 MVC로 어떻게 조회하는지 알아보자.
