---
title: indexedDB中遇到的问题
date: 2021-05-24 17:20:25
categories: 报错
tags:
  - 报错
  - indexedDB
---

1.  index 查找时不能使用布尔值

原本`isReverse`传的是布尔，会报错
`DataError: Failed to execute 'get' on 'IDBIndex': The parameter is not a valid key.`

```js
const indexedDBRead = (id, isReverse) => {
  return new Promise((resolve, reject) => {
    const dbRequest = window.indexedDB.open('blackbox', 1)
    dbRequest.onsuccess = (e) => {
      const transaction = e.target.result.transaction(
        ['tempContacts'],
        'readwrite'
      )
      const objectStore = transaction.objectStore('tempContacts')
      const index = objectStore.index('myIndex')

      const request = index.get([id, false])

      request.onerror = function (event) {
        reject()
      }

      request.onsuccess = function (event) {
        resolve(request.result)
      }
    }
  })
}
```

2. 所有的事务，要在 onsuccess 中写，如下

```js
const indexedDBAdd = (obj) => {
  return new Promise((resolve, reject) => {
    const dbRequest = window.indexedDB.open('blackbox', 1)
    dbRequest.onsuccess = (e) => {
      const transaction = dbRequest.result.transaction(
        ['tempContacts'],
        'readwrite'
      )
      const objectStore = transaction.objectStore('tempContacts')
      const request = objectStore.add(obj)
      request.onsuccess = (event) => {
        console.log('数据写入成功')
        resolve(true)
      }

      request.onerror = (event) => {
        console.log('数据写入失败')
        reject()
      }
    }
  })
}
const indexedDBDel = () => {
  const dbRequest = window.indexedDB.open('blackbox', 1)
  dbRequest.onsuccess = (e) => {
    const transaction = dbRequest.result.transaction(
      ['tempContacts'],
      'readwrite'
    )
    const objectStore = transaction.objectStore('tempContacts')
    objectStore.clear()
    console.log('已删除')
  }
}
```
