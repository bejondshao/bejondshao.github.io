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
		<mapper resource="mybatis/xml/GuestInfoMapper.xml" />
	</mappers>
```
@ 添加GuestInfoMapper接口
```
    public interface GuestInfoMapper {
	void updateGuestInfoByGuestNumber(UpdateGuestInfo updateGuestInfo);
```
@ 编写model类，用于包装参数

```
package mybatis.model;
public class UpdateGuestInfo {
	private String guestNumber;
	private String firstName;
	private String lastName;
	private String documentType;
	private String documentTypeColumn;
```
@ 添加Mapper.xml，编写sql
GuestInfoMapper.xml
```
<update id="updateGuestInfoByGuestNumber" parameterType="cn.shijinet.kunlun.kiosk.mybatis.model.UpdateGuestInfo">
		UPDATE NAME
		SET
			xlast_name = #{lastName},
			xfirst_name = #{firstName},
			sxname = #{lastName},
			sxfirst_name = #{firstName},
			${documentTypeColumn} = #{documentType}
		WHERE
			name_id = #{guestNumber}
	</update>
```
@ 调用接口方法，注意update时需要commit
```
SqlSession session = sqlSession.get();
		GuestInfoMapper mapper = session.getMapper(GuestInfoMapper.class);
		mapper.updateGuestInfoByGuestNumber(updateGuestInfo);
		session.commit();
```