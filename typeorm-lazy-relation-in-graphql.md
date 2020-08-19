# graphql resolver에서 Typeorm의 lazy-relation 기능을 이용할 때 실패 경험
typeorm의 lazy-relation 기능은 graphql의 특징과 잘 부합한다고 판단하여 도입하였으나, 해당 기능이 [graphql의 n+1 현상](https://godsenal.github.io/2018/10/22/Graphql-2-N-1-%EB%AC%B8%EC%A0%9C%EC%99%80-Dataloader/)을 초래함.

## 문제해결
entity(혹은 custom repository)에 dataloader를 적용하고, lazy-relation을 원래대로 되돌려 해결할 수 있었음.
