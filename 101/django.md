# 장고 101

## 설계철학과 디자인패턴

장고의 근본적인 목표는 느슨한 결합, 탄탄한 응집이다. 즉 각 계층은 서로의 작동방식을 모른 채 독립적으로 작동해야한다. DRY(Don't Repeat Yourself)는 최대한 정규화를 해서 중복을 줄이는 것인데, 적은 코드와 빠른 개발을 가능하게 한다. 추가로 큰 편익이 있지 않은 이상 명시적으로 프레임워크를 사용하게끔 했으며, 저수준의 파이썬 코딩부터 고수준의 장고를 일관적으로 사용할 수 있게 한다.

장고의 디자인패턴은 MTV(Model, Template, View)인데 이는 각각 MVC(Model, View, Controller)에 대응된다. Model 은 데이터베이스에 저장되는 데이터 형식이고, 장고는 ORM 을 통해 데이터 처리 로직까지 제공한다. Template 은 요청에 대한 결과물을 보여준다. 뷰에서 데이터를 받아 장고 템플릿 문법으로 나타낼 수 있다. View 는 사용자 요청을 받아 모델에 데이터를 요청하고, 다시 가져온 데이터를 가공하여 템플릿에 전달한다.


## Queryset

장고의 ORM(Object Relational Mapping) 은 관계형 데이터베이스를 객체로 매핑하여 편리하게 접근을 가능하게끔 한다. DBMS 을 바꾸더라도 ORM 코드는 그대로 사용이 가능하다는 장점이 있지만 복잡한 SQL 을 구성할 때 어려우며, 성능의 하락이 발생할 수 있다. 

객체를 만들때는 create() 나 save() 를 사용해 INSERT 문을 실행하고, UPDATE 는 save() 문을 사용한다. 장고는 객체의 pk 가 있는지 없는지에 따라 save() 를 호출할 때 INSERT 와 UPDATE 를 자동으로 구별한다. 또는 save(update_fields=[..])로 update 를 강제하면서 쿼리를 단순화 할 수 있다. fk 로 연결된 필드도 `user_obj.team = team_obj` 나 `team_obj.users.add(user_obj, ..)` 처럼 객체를 통해 갱신을 할 수 있다. 

데이터베이스를 조회하는 경우 장고는 모델 클래스의 Manager 에 따라 Queryset 을 반환한다. Manager 는 `User.objects.all()` 의 `objects` 가 디폴트 매니저이며, 데이터베이스를 조회하고 레코드를 가져온다. 사용자 정의 매니저를 생성하거나 디폴트 매니저에 새로 함수를 추가해서도 사용할 수 있다. Queryset 은 sql 쿼리를 저장해놓고 필요할 때에 데이터베이스에서 결과를 가져오는 객체이다. 쿼리를 저장할 수 있기 때문에 연쇄적으로 함수를 호출할 수 있고, 따라서 객체 생성 시 sql 문이 실행되는 것이 아니라 Lazy Loading(지연로딩) 방식으로 필요할 때에 데이터베이스에 접근한다. `user_set = User.objects.all()` 생성 후 `user_set[0]` 을 호출하면 쿼리는 한번만 실행된다. 하지만 다르게 생각하면 필요할 때마다 DB 에 접근하는 셈이 되어버리기 때문에, `user_set` 호출 후 `user_set[0]` 을 호출하면 각각 두번의 sql 문을 요청한다. 따라서 코드를 진행하는 동안 데이터에 변경이 없다는 것을 확신할 수 있다면 `user_set = list(User.objects.all())` 로 쿼리 결과를 저장한 후에 접근하는 것이 호출을 줄일 수 있다. 쿼리셋 객체를 중첩할 경우에도 속도가 느려질 수 있기 때문에 `team_set = Team.objects.filter(name__icontains='tech').values_list('pk',flat=True)` 이후 `user_set = User.objects.filter(team__in=list(team_set))` 과 같이 접근하는 것이 좋다.

자주 사용하는 쿼리셋 API 는 all(), get(), filter(), exclude(), order_by() 이 있다. 객체를 하나만 가져올 때 filter 는 쿼리셋이라서 호출 할 때마다 DB 에 접근하므로, get() 을 사용한다. filter 는 fk 로 연결된 테이블 조회 시 구현에 따라 다른 결과가 나온다. `Team.objects.filter(user__name__contains='D', user__age__gte=25)`의 경우 두 조건을 동시에 갖는 유저가 있는 팀만 반환한다(INNER JOIN 후 WHERE .. AND ..). 하지만 `Team.objects.filter(user__name__contains='D').filter(user__age__gte=25)` 는 팀에 조건 1에 속하는 유저와 조건 2에 속하는 유저가 각각 있어도 해당 팀을 반환한다(INNER JOIN 두번 수행). 

* exists: 쿼리셋에 결과물이 있는지 없는지를 반환한다. 가능한 한 빠르고 간단한 sql 문을 내부적으로 작성한다.

* 캐시: 쿼리셋에 결과물을 저장해서 캐시처럼 사용하려면, 쿼리셋에 들어있는 객체를 한번씩 조회해야한다. `[entry for entry in queryset]`


## N+1 문제, select_related, prefetch_related

지연로딩은 fk 로 연결된 필드에 대해 N+1 문제를 일으킨다. N+1 문제는 가져온 쿼리셋에 대해 루프를 돌면서 다시 N 번 DB 에 접근하는 것이다. `for user_obj in User.objects.all(): print(user_obj.team.name)` 과 같은 경우이다. 이를 해결하기 위해 select_related 과 prefetch_related 로 연관되어있는 객체를 미리 가져오는 Eager Loading 방식을 사용한다.

select_related 는 1:1 관계에서 사용하거나, 1:N 관계에서 N 쪽이 사용할 수 있다. `User.objects.select_related('team__name').all()` 을 사용하면 user 와 team 테이블이 INNER JOIN 이 되고(team 모델이 fk 를 가지면 LEFT OUTER JOIN), 한번에 team 테이블의 컬럼까지 가져와서 캐싱하기 때문에, 위의 N+1 문제를 해결할 수 있다. team 이 company 컬럼을 fk 로 갖는 경우여도 `User.objects.select_related('team__company').all()` 로 최적화가 가능하다. 이처럼 중첩된 JOIN 을 진행할 때나, on_delete=SET_NULL 옵션이 있을 경우에는 LEFT OUTER JOIN 을 수행한다.

select_related 는 sql 문으로 JOIN 을 진행하기 때문에, 다대다 관계에서는 쿼리의 양이 많아지고 결과 테이블도 커진다. 이를 보완하기 위해 prefetch_related 는 각 관계에 대한 sql 문을 요청해서 파이썬 내부에서 조인을 한다. 다대다 관계인 user 와 hobby 테이블이 있을 때, 장고는 자동으로 user_hobby 테이블을 만든다. `user_obj.hobbies.all()` 로 접근을 하면 hobby 와 user_hobby 테이블을 조인하여 user_obj.id 와 같은 레코드만 가져온다. 모든 유저에 대한 취미 정보를 가져오려면 매 유저 id 마다 user_hobby 테이블을 조인하고 where 문을 수행하는 N+1 의 비효율적인 작업을 한다. `User.objects.prefetch_related('hobbies').all()` 은 유저 아이디 정보를 가져오고, user_hobby 조인 결과에서 해당 아이디가 포함된 정보만 가져오게끔해서 sql 문을 2번으로 줄인다. `for h in user_obj.hobbies.all(): print(h.name)` 을 수행할 경우 캐시된 정보를 사용하므로 DB 요청을 보내지 않는다. 중첩된 다대다 관계도 사용할 수 있지만, 조인된 결과가 캐시에 저장될수록 그만큼의 메모리를 사용하기에 주의해야한다. 

* Prefetch(lookup, queryset, to_attr): prefetch_related 에 추가 조건을 줄 수 있다. `Team.objects.prefetch_related(Prefetch('user_set__hobbies', queryset=Hobby.objects.filter(name='swimming')))` team 을 가져오고 team id 에 속하는 user 를 가져온다. hobby 테이블과 user_hobby 테이블을 JOIN 하고 이름이 swimming 이면서 앞서 가져온 user id 에 속하는 경우를 가져온다.

* N+1 문제 디버깅툴: https://scoutapm.com/blog/django-and-the-n1-queries-problem

## bulk_update, select_for_update

객체의 개수에 상관없이 쿼리 한번으로 UPDATE 문을 진행한다. `SET 'name' = CASE WHEN (id=1) THEN 'newname' .. ` 과 같은 sql 을 작성하며, 레코드가 많으면 sql 문도 길어지기 때문에 이를 방지하기 위해서 batch_size 파라미터를 사용한다. fk 로 연결된 부분이 수정되면 추가적인 쿼리가 생성되고, 때문에 bulk_create 는 다대다 관계에서 작동하지 않는다. lock 이 걸리지 않아 race condition 의 영향을 받을 수 있다.

select_for_update 는 `SELECT ... FOR UPDATE` sql 문을 생성해, 트랜잭션이 끝날때까지 쿼리셋이 가리키는 데이터에 lock 을 건다. 다른 요청이 들어오면 block 되고, 트랜잭션이 끝난 후에 데이터에 접근할 수 있다. nowait=True 로 block 이 되지 않고 에러를 발생시킬 수 있는데, 트랜잭션이 너무 오래 걸릴 때나, lock 이 잡힌 데이터를 건너뛰고 다른 데이터에 접근하려고 할 때 설정할 수 있다. skip_locked=True 는 다른 요청이 들어왔을 때 lock 이 걸려있지 않은 행들에 대해 다른 프로세스의 접근을 허용한다. lock 은 모델과 fk 로 연관된 다른 테이블에까지 lock 을 거는데, of=('self',..) 로 lock 을 걸 테이블을 설정할 수 있다. UPDATE 를 수행하는 동안 다른 프로세스가 fk 테이블의 pk 값을 수정하면 문제가 생기기 때문에 fk 로 연결된 키 이외의 값을 수정할 수 있게끔 한다.