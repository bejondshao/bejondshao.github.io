layout: post
title: Mybatis更新数据库示例
date: 2017-03-30 17:56:20
tags:
- Java
- Mybatis
category:
- Java
---
@ mybatis-config.xml
```
    <mappers>
		<mapper resource="mybatis/xml/UserMapper.xml" />
	</mappers>
```
@ 添加GuestInfoMapper接口
```
    public interface UserMapper {
	void updateUserByUserNumber(UpdateUser updateUser);
```
@ 编写model类，用于包装参数

```
package mybatis.model;
public class UpdateUser {
	private String userNumber;
	private String firstName;
	private String docType;
	private String docTypeColumn;
```
@ 添加Mapper.xml，编写sql
UserMapper.xml
```
<update id="updateUserByUserNumber" parameterType="mybatis.model.UpdateUser">
		UPDATE NAME
		SET
			firstname = #{firstName},
			sfirstname = #{firstName},
			${docTypeColumn} = #{docType}
		WHERE
			name_id = #{userNumber}
	</update>
```
@ 调用接口方法，注意update时需要commit
```
SqlSession session = sqlSession.get();
		UserMapper mapper = session.getMapper(UserMapper.class);
		mapper.updateUserByUserNumber(updateUser);
		session.commit();
```