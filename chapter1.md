# 리팩토링하기

### 리팩터링이란 무엇인가?

- 코드를 더빠르게 만들기 위해
- 더 작은 코드를 만들기 위해
- 코드를 더 일반적이거나 재사용 가능하게 하기 위해
- 코드의 가독성을 높이고 유지보수를 용이하게 하기 위해

> 좋은 코드 - 사람이 읽기 쉽고, 유지보수가 용이하며, 의도한 대로 잘 동작하는 코드

> 리팩터링 - 기능을 변경하지 않고 코드의 가독성과 유지보수가 쉽도록 코드를 변경하는것

> 리팩터링은 좋은 코드를 만들기 위해 변경하는것!

```
리팩터링을 했는데 잘못된 경우(잘못된 적용)는/은 어떤게 있을까?
```

# 리팩터링 깊게 들여다 보기

- 불변속성이 유효한 상태로 유지되기란 거의 불가능하다 p17

```
범위제한 말고 불변속성을 지키는 방법은 무엇이 있을까?
```

- 상속 보다는 컴포지션을 사용하라

# 긴 코드 조각내기

- 다섯 줄 제한으로 긴 메서드 식별하기
- 메서드 추출으로 추상화 수준 맞추기
- 호출 또는 전달 한가지만 할것
- if문은 함수의 시작에만 배치

```
들여쓰기를 줄이기 위해서 일까?
```

# 실습

```ts
async getComments(
    dto: RequestGetCommentsDto,
  ): Promise<ResponseGetCommentsDto> {
    const { idx, id, page, size } = dto;
    const total = await this.commentRepository.getCommentTotalSize(idx, id);
    const offset = (page - 1) * size;
    const totalPage = Math.ceil(total / size);

    const commentsEntities = await this.commentRepository.getComments({size,offset,id,idx});
    const comments: CommentDto[] = [];

    const comment = {
      idx: commentsEntities.idx,
      post: commentsEntities.post,
    };

   for await (const comment of commentsEntities) {
        const c = await this.cRepository.findCollectionById(code);
        const i = await this.iRepository.findCreatorById(code);
      }
      comments.push(comment),
      );
    return {
      comments,
      page: Number(page),
      total_size: Number(total),
      total_page: Number(totalPage),
    };
  }
```

```ts
async getComments(
    dto: RequestGetCommentsDto,
  ): Promise<ResponseGetCommentsDto> {
    const { idx, id, page, size } = dto;

    const total = await this.commentRepository.getCommentTotalSize(idx, id);

    const offset = (page - 1) * size;
    const totalPage = Math.ceil(total / size);

    const commentsEntities = await this.commentRepository.getComments({
      size,
      offset,
      id,
      idx,
    });
    const comments: Promise<CommentDto[]> =
      this.getCommentsEntity(commentsEntities);

    return {
      comments,
      page: Number(page),
      total_size: Number(total),
      total_page: Number(totalPage),
    };
  }

   async getCommentsEntity(commentsEntities): Promise<CommentDto[]> {
    const comments: CommentDto[] = [];

    for await (const comment of commentsEntities) {
        const c = await this.cRepository.findCollectionById(comment.cCode);
        const i = await this.iRepository.findCreatorById(comment.iCode);
      }
      comments.push(
        this.articleMapper.toCommentsDto(
          comment
        ),
      );
    return comments;
  }
```
