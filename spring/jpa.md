# jpa

- spring Data jpa <--> Hibernate <---> jdbc <---> db

## CRUD

- insert = save(entity)
- select = findById(key) , getOne(key) // getOne 호출 시 쿼리 수행 (transactional)
- update = save(entity)
- delete = deleteById(key), delete(entiy)
