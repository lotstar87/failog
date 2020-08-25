# typeorm transaction 내에서 error를 catch한 후 다시 transaction을 발생시키려는 과정에서 실패

## 증상
아래의 코드를 실행할 때 try에서 transaction이 abort된 상태가 되고, catch에서 다시 transaction을 발생시키려 시도하면서 error가 발생
```ts
await getConnection().transaction(async txManager => {
  await txManager.doSomething()

  try {
    txManager.doSomething() // throw error
  } catch (e) {
    txManager.doSomething() // transaction is already aborted
  }
})
```

## 해결방법
try-catch 밖과 try, catch 내에서 각각 transaction을 생성하고 실행하는 방법으로 해결
```ts
await getConnection().transaction(async txManager => {
  await txManager.doSomething()

  await getManager().transaction(async () => {
    try {
      await getManager().transaction(async innerTxManager1 => {
        innerTxManager1.doSomething() // throw error
      })
    } catch (e) {
      await getManager().transaction(async innerTxManager2 => {
        innerTxManager2.doSomething()
      })
    }
  })
})
```
