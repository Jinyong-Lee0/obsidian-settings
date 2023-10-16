---
created: 2023-10-14 T19:06
updated: 2023-10-14 T22:25
---
# DQL, JS 및 인라인

관련 페이지에 [유용한 데이터를 추가](../annotation/add-metadata-ko.md)한 후에는 실제로 데이터를 어딘가에 표시하거나 조작해야 할 것입니다. Dataview를 사용하면 이를 위해 4가지 다른 방법을 제공합니다. 이 모든 방법은 Markdown에서 직접 코드 블록으로 작성되며, 볼트가 변경될 때 실시간으로 다시로드됩니다.

## Dataview 쿼리 언어 (DQL)

[**Dataview 쿼리 언어**](../../queries/structure-ko.md)(간단히 **DQL**)는 SQL과 유사한 언어로, Dataview의 핵심 기능입니다. [네 가지 쿼리 유형](./query-types-ko.md)을 지원하여 다른 출력물을 생성하고, 결과를 정제하거나 정렬 또는 그룹화하는 [데이터 명령어](./data-commands-ko.md)와 [다양한 함수](../reference/functions-ko.md)를 사용하여 원하는 출력물을 만들 수 있습니다.

!!! warning SQL과의 차이점
    SQL에 익숙하신 분들은 DQL을 SQL과 혼동하지 않도록 하기 위해 [SQL과의 차이점](../../queries/differences-to-sql-ko.md)을 읽어주시기 바랍니다.

`dataview` 타입으로 지정된 코드 블록을 사용하여 **DQL** 쿼리를 작성합니다:

~~~
```dataview
TABLE rating AS "Rating", summary AS "Summary" FROM #games
SORT rating DESC
```
~~~

!!! attention "역따옴표 사용"
    유효한 코드 블록은 시작과 끝에 역따옴표(\`)를 사용해야 합니다. 역따옴표와 비슷하게 보이는 작은 따옴표 '와 혼동하지 마세요!

DQL 쿼리 작성 방법에 대한 설명은 [쿼리 언어 참조](../../queries/structure-ko.md)에서 확인할 수 있습니다. 예제로 배우는 것이 더 좋다면 [쿼리 예제](../../resources/examples-ko.md)를 살펴보세요.

## 인라인 DQL

인라인 DQL은 코드 블록 대신 인라인 블록 형식을 사용하며, 지정된 접두사를 통해 이 인라인 코드 블록을 DQL 블록으로 표시합니다.

~~~
`= this.file.name`
~~~

!!! info "DQL 접두사 변경"
    설정에서 `=`를 다른 토큰(예: `dv:` 또는 `~`)으로 변경할 수 있습니다. 설정 위치: Dataview > Codeblock Settings > Inline Query Prefix

인라인 DQL 쿼리는 중간에 하나의 값만 표시되며, 주변 내용과 완벽하게 조화를 이룹니다.

~~~
오늘은 `= date(today)` - `= [[exams]].deadline - date(today)`까지 시험이 남았습니다!
~~~

예를 들어, 위의 내용은 다음과 같이 렌더링됩니다.

~~~
오늘은 2022년 11월 7일 - 시험이 2개월 5일 남았습니다!
~~~

**인라인 DQL**은 여러 페이지를 쿼리할 수 없습니다. 항상 하나의 값만 표시되며, 값 목록(또는 테이블)이 아닙니다. `this.` 접두사를 사용하여 **현재 페이지**의 속성에 액세스하거나, `[[다른페이지]]`를 사용하여 다른 페이지에 액세스할 수 있습니다.

~~~
`= this.file.name`
`= this.file.mtime`
`= this.someMetadataField`
`= [[secondPage]].file.name`
`= [[secondPage]].file.mtime`
`= [[secondPage]].someMetadataField`
~~~

인라인 DQL 쿼리에서는 [식](../../reference/expressions-ko.md)과 [리터럴](../../reference/literals-ko.md)을 포함한 모든 것을 사용할 수 있으며, [함수](../../reference/functions-ko.md)도 사용할 수 있습니다. 그러나 쿼리 유형(Query Types)과 데이터 명령어(Data Commands)는 **인라인에서 사용할 수 없습니다.**

~~~
과제 마감까지 `= this.due - date(today)`
기말 논문 마감까지 `= [[컴퓨터 과학 이론]].due - date(today)`

🏃‍♂️ 목표 달성? `= choice(this.steps > 10000, "YES!", "**No**, get moving!")`

할 일이 `= length(filter(link(dateformat(date(today), "yyyy-MM-dd")).file.tasks, (t) => !t.completed))`개 남았습니다. `= choice(date(today).weekday > 5, "Take it easy!", "Time to get work done!")`
~~~

## Dataview JS

Dataview [JavaScript API](../../api/intro-ko.md)를 사용하면 JavaScript의 모든 기능을 활용할 수 있으며, Dataview 데이터를 추출하고 쿼리를 실행하는 DSL을 제공하여 임의로 복잡한 쿼리와 뷰를 생성할 수 있습니다. 쿼리 언어와 마찬가지로 `dataviewjs`로 주석이 달린 코드 블록을 사용하여 Dataview JS 블록을 생성합니다:

```java
```dataviewjs
let pages = dv.pages("#books and -#books/finished").where(b => b.rating >= 7);
for (let group of pages.groupBy(b => b.genre)) {
   dv.header(3, group.key);
   dv.list(group.rows.file.name);
}
```

JS dataview 블록 내부에서는 `dv` 변수를 통해 전체 dataview API에 액세스할 수 있습니다. 이를 통해 수행할 수 있는 작업에 대한 설명은 [API 문서](../../api/code-reference-ko.md)나 [API 예제](../../api/code-examples-ko.md)를 참조하세요.

!!! attention "고급 사용법"
    JavaScript 쿼리 작성은 프로그래밍과 JavaScript에 대한 이해가 필요한 고급 기술입니다. 다른 사람의 JavaScript 쿼리를 사용할 때는 주의가 필요하며, 특히 Obsidian 커뮤니티를 통해 공개되지 않은 경우에는 파일 시스템에 액세스할 수 있으므로 신중하게 사용해야 합니다.

## 인라인 Dataview JS

쿼리 언어와 마찬가지로 인라인 JS 쿼리도 작성할 수 있으며, 계산된 JS 값을 직접 포함시킬 수 있습니다.
인라인 코드 블록을 사용하여 JS 인라인 쿼리를 생성합니다:

```
`$= dv.current().file.mtime`
```

인라인 DataviewJS에서는 `dataviewjs` 코드 블록과 마찬가지로 `dv` 변수에 액세스할 수 있으며, 동일한 호출을 수행할 수 있습니다. 결과는 JavaScript 값으로 평가되어 Dataview에서 적절하게 렌더링됩니다.

인라인 JS 쿼리는 인라인 DQL 쿼리와 달리 Dataview JS 쿼리에서 사용 가능한 모든 기능에 액세스할 수 있으며, 여러 페이지를 쿼리하고 출력할 수도 있습니다.

!!! info "Inline JS 접두사 변경"
    설정에서 `$=`를 다른 토큰(예: `dvjs:` 또는 `$~`)로 변경할 수 있습니다. 설정 위치: Dataview > Codeblock Settings > Javascript Inline Query Prefix