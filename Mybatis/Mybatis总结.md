# һ��Mybatis���
## 1.1 ���
- MyBatis ��֧�ֶ��ƻ� SQL���洢�����Լ��߼�ӳ�������ĳ־ò��ܡ�

- MyBatis �����˼������е� JDBC ������ֶ����ò����Լ���ȡ�������

- MyBatis����ʹ�ü򵥵�XML��ע���������ú�ԭʼӳ�䣬���ӿں�Java��POJO��Plain Old Java Objects����ͨ��Java����ӳ������ݿ��еļ�¼.

## 1.2 ΪʲôҪʹ��MyBatis��
- MyBatis��һ�����Զ����ĳ־û����ܡ�
- JDBC
  - SQL����Java��������϶ȸߵ���Ӳ��������
  - ά��������ʵ�ʿ���������sql���б仯��Ƶ���޸ĵ�������
- Hibernate��JPA
  - ���Ѹ���SQL������Hibernate���Դ���Ҳ������
  - �ڲ��Զ�������SQL���������������Ż���
  - ����ȫӳ���ȫ�Զ���ܣ������ֶε�POJO���в���ӳ��ʱ�Ƚ����ѡ�
  - �������ݿ������½���
- �Կ�����Ա���ԣ�����sql������Ҫ�Լ��Ż�
- ==sql��java����ֿ������ܱ߽�������һ��רעҵ��һ��רע����==��


# ����MyBatis-ȫ�������ļ�
MyBatis �������ļ�������Ӱ��MyBatis��Ϊ��������ã�settings�������ԣ�properties����Ϣ���ĵ��Ķ���ṹ���£�
- configuration ����
  - properties ����
  - settings ����
  - typeAliases ��������
  - typeHandlers ���ʹ�����
  - objectFactory ���󹤳�
  - plugins ���
  - environments ����
    - environment ��������
      - transactionManager ���������
      - dataSource ����Դ
  - databaseIdProvider ���ݿ⳧�̱�ʶ
  - mappers ӳ����

## 2.1 properties ����
![image](.img/mybatis-1.png)
��������ڲ�ֻһ���ط����������ã���ô MyBatis �����������˳�������أ�
- �� properties Ԫ������ָ�����������ȱ���ȡ��
- Ȼ����� properties Ԫ���е� resource ���Զ�ȡ��·���������ļ������ url ����ָ����·����ȡ�����ļ����������Ѷ�ȡ��ͬ�����ԡ�
- ����ȡ��Ϊ�����������ݵ����ԣ��������Ѷ�ȡ��ͬ�����ԡ�


## 2.2 settings����
���� MyBatis �м�Ϊ��Ҫ�ĵ������ã����ǻ�ı� MyBatis ������ʱ��Ϊ��
![image](.img/mybatis-2.png)

![image](.img/mybatis-3.png)

## 2.3 typeAliases ��������
���ͱ�����Ϊ Java ��������һ���̵����֣����Է�����������ĳ���ࡣ

```
		<!-- 1��typeAlias:Ϊĳ��java���������
				type:ָ��Ҫ�����������ȫ����;Ĭ�ϱ�����������Сд��employee
				alias:ָ���µı���
		 -->
		<!-- <typeAlias type="com.example.mybatis.bean.Employee" alias="emp"/> -->
```
��ܶ������£������������ñ���������µ�ÿһ���ഴ��һ��Ĭ�ϵı��������Ǽ�����Сд��

```
		<!-- 2��package:Ϊĳ�����µ���������������� 
				name��ָ��������Ϊ��ǰ���Լ��������еĺ������ÿһ���඼��һ��Ĭ�ϱ���������Сд������
		-->
		<package name="com.example.mybatis.bean"/>
```
Ҳ����ʹ��@Aliasע��Ϊ��ָ��һ������
```
@Alias("emp")
public class Employee {
...
```
ֵ��ע����ǣ�MyBatis�Ѿ�Ϊ��ೣ����Java�����ڽ�����Ӧ�����ͱ��������Ƕ��Ǵ�Сд�����еģ��������������ʱ��ǧ��Ҫռ�����еı�����
![image](.img/mybatis-5.png)

## 2.4 typeHandlers���ʹ�����
������ MyBatis ��Ԥ������䣨PreparedStatement��������һ������ʱ�����Ǵӽ������ȡ��һ��ֵʱ�� ����==�����ʹ���������ȡ��ֵ�Ժ��ʵķ�ʽת���� Java ����==��
![image](.img/mybatis-6.png)

## 2.5 �������͵Ĵ���
- ���ں�ʱ��Ĵ���JDK1.8��ǰһֱ�Ǹ�ͷ�۵����⡣����ͨ��ʹ��JSR310�淶�쵼��Stephen Colebourne������Joda-Time��������1.8�Ѿ�ʵ��ȫ����JSR310�淶�ˡ�
- ����ʱ�䴦���ϣ����ǿ���ʹ��MyBatis����JSR310��Date and Time API����д�ĸ�������ʱ�����ʹ�������
- MyBatis3.4��ǰ�İ汾��Ҫ�����ֶ�ע����Щ���������Ժ�İ汾�����Զ�ע��ġ�
  ![image](.img/mybatis-7.png)

## 2.6 �Զ������ʹ�����
���ǿ�����д���ʹ������򴴽��Լ������ʹ�����������֧�ֵĻ�Ǳ�׼�����͡�

**���裺**
? 1����ʵ��org.apache.ibatis.type.TypeHandler�ӿڻ��߼̳�org.apache.ibatis.type.BaseTypeHandler
? 2����ָ����ӳ��ĳ��JDBC���ͣ���ѡ������
? 3������mybatisȫ�������ļ���ע��

## 2.7 plugins���
�����MyBatis�ṩ��һ���ǳ�ǿ��Ļ��ƣ����ǿ���ͨ��������޸�MyBatis��һЩ������Ϊ��==���ͨ����̬�������==�����Խ����Ĵ������κ�һ��������ִ�С��������ר�ŵ��½�����������mybatis����ԭ���Լ����.

- Executor (update, query, flushStatements, commit, rollback,
  getTransaction, close, isClosed)
- ParameterHandler (getParameterObject, setParameters)
- ResultSetHandler (handleResultSets, handleOutputParameters)
- StatementHandler (prepare, parameterize, batch, update, query)

## 2.8 environments����
MyBatis�������ö��ֻ��������翪�������Ժ�����������Ҫ�в�ͬ�����á�

ÿ�ֻ���ʹ��һ��environment��ǩ�������ò�ָ��Ψһ��ʶ��

����ͨ��environments��ǩ�е�default����ָ��һ�������ı�ʶ�������ٵ��л�����

### 2.8.1 environment-ָ�����廷��
? id��ָ����ǰ������Ψһ��ʶ
? transactionManager����dataSource��������
![image](.img/mybatis-8.png)

#### 2.8.1.1 transactionManager
type�� JDBC | MANAGED | �Զ���
- JDBC��ʹ���� JDBC ���ύ�ͻع����ã������ڴ�����Դ�õ�����������������Χ��
  JdbcTransactionFactory
- MANAGED�����ύ��ع�һ�����ӡ�����������������������������ڣ����� JEE Ӧ�÷������������ģ��� ManagedTransactionFactory
- �Զ��壺ʵ��TransactionFactory�ӿڣ�type=ȫ����/����

#### 2.8.1.2 dataSource
type�� UNPOOLED | POOLED | JNDI | �Զ���
- UNPOOLED����ʹ�����ӳأ�UnpooledDataSourceFactory
- POOLED��ʹ�����ӳأ� PooledDataSourceFactory
- JNDI�� ��EJB ��Ӧ�÷��������������в���ָ��������Դ
- �Զ��壺ʵ��DataSourceFactory�ӿڣ���������Դ�Ļ�ȡ��ʽ��

==ʵ�ʿ���������ʹ��Spring��������Դ��������������Ƶ�������������������==

## 2.9 databaseIdProvider����
MyBatis ���Ը��ݲ�ͬ�����ݿ⳧��ִ�в�ͬ����䡣
![image](.img/mybatis-9.png)

- Type�� DB_VENDOR
  - ʹ��MyBatis�ṩ��VendorDatabaseIdProvider�������ݿ⳧�̱�ʶ��Ҳ����ʵ��DatabaseIdProvider�ӿ����Զ��塣
- Property-name�����ݿ⳧�̱�ʶ
- Property-value��Ϊ��ʶ��һ������������SQL���ʹ��databaseId��������
  ![image](.img/mybatis-10.png)

DB_VENDOR
- ��ͨ�� DatabaseMetaData#getDatabaseProductName()���ص��ַ����������á�����ͨ�����������ַ������ǳ���������ͬ��Ʒ�Ĳ�ͬ�汾�᷵�ز�ͬ��ֵ���������ͨ���������Ա�����ʹ���̡�

**MyBatisƥ��������£�**
- 1�����û������databaseIdProvider��ǩ����ôdatabaseId=null
- 2�����������databaseIdProvider��ǩ��ʹ�ñ�ǩ���õ�nameȥƥ�����ݿ���Ϣ��ƥ��������databaseId=����ָ����ֵ����������Ϊnull
- 3�����databaseId��Ϊnull����ֻ���ҵ�����databaseId��sql���
- 4��MyBatis ����ز��� databaseId ���Ժʹ���ƥ�䵱ǰ���ݿ�databaseId ���Ե�������䡣���ͬʱ�ҵ�����databaseId�Ͳ���databaseId����ͬ��䣬����߻ᱻ������


## 2.10 mapperӳ��
**mapper���ע��SQLӳ���ļ�**
![image](.img/mybatis-11.png)

**����ʹ������ע�᣺**
? ���ַ�ʽҪ��SQLӳ���ļ�������ͽӿ�����ͬ������ͬһĿ¼��
![image](.img/mybatis-12.png)

# �ġ�MyBatis-ӳ���ļ�
ӳ���ļ�ָ����MyBatis��ν������ݿ���ɾ�Ĳ飬���ŷǳ���Ҫ�����壻

- cache �C�����ռ�Ķ�����������
- cache-ref �C ���������ռ仺�����õ����á�
- resultMap �C �Զ�������ӳ��
- parameterMap �C �ѷ�������ʽ���Ĳ���ӳ��
- sql �C��ȡ���������顣
- insert �C ӳ��������
- update �C ӳ��������
- delete �C ӳ��ɾ�����
- select �C ӳ���ѯ���

## 4.1 insert��update��deleteԪ��
![image](.img/mybatis-13.png)

## 4.2 �������ɷ�ʽ
�����ݿ�֧���Զ������������ֶΣ����� MySQL �� SQL Server�������������
useGeneratedKeys=��true����Ȼ���ٰ�keyProperty ���õ�Ŀ�������ϡ�
![image](.img/mybatis-14.png)

�����ڲ�֧�����������������ݿ⣨����Oracle���������ʹ�� selectKey ��Ԫ�أ�
selectKey Ԫ�ؽ����������У�id �ᱻ���ã�Ȼ��������ᱻ���á�
![image](.img/mybatis-15.png)

### selectKey
![image](.img/mybatis-16.png)

## 4.3 ������Parameters������
- ��������
  - ���Խ��ܻ������ͣ��������ͣ��������͵�ֵ���������MyBatis��ֱ��ʹ���������������Ҫ�����κδ���
- �������
  - ���������������ᱻMyBatis���°�װ��һ��Map���롣
    Map��key��param1��param2��0��1����ֵ���ǲ�����ֵ��
- ��������
  - Ϊ����ʹ��@Param��һ�����֣�MyBatis�ͻὫ��Щ������װ��map�У�key���������Լ�ָ��������
- POJO
  - ����Щ������������ҵ��POJOʱ������ֱ�Ӵ���POJO
- Map
  - ����Ҳ���Է�װ�������Ϊmap��ֱ�Ӵ���

### 4.3.1 ��������
**(1)����Ҳ����ָ��һ��������������ͣ�**
![image](.img/mybatis-17.png)
- javaType ͨ�����ԴӲ�����������ȥȷ��
- ��� null ������ֵ�����ݣ��������п���Ϊ�յ��У�
  jdbcType ��Ҫ������
- ������ֵ���ͣ�����������С���������λ����
- mode ��������ָ�� IN��OUT��INOUT�������������ΪOUT��INOUT�������������Ե���ʵֵ���ᱻ�ı䣬�����ڻ�ȡ�������ʱ��������������

**(2)����λ��֧�ֵ�����**
�C javaType��jdbcType��mode��numericScale��resultMap��typeHandler��jdbcTypeName��expression

ʵ����ͨ�������õ��ǣ�
����Ϊ�յ�����ָ�� jdbcType
? #{key}����ȡ������ֵ��Ԥ���뵽SQL�С���ȫ��
? ${key}����ȡ������ֵ��ƴ�ӵ�SQL�С���SQLע�����⡣ORDER BY ${name}

### 4.3.2 selectԪ��
SelectԪ���������ѯ������

? Id��Ψһ��ʶ����
�C ��������������䣬��Ҫ�ͽӿڵķ�����һ��
? parameterType���������͡�
�C ���Բ�����MyBatis�����TypeHandler�Զ��ƶ�
? resultType������ֵ���͡�
�C ��������ȫ������������ص��Ǽ��ϣ����弯����Ԫ�ص����͡����ܺ�resultMapͬʱʹ��

![image](.img/mybatis-18.png)

### 4.3.3 �Զ�ӳ��
#### 1��ȫ��setting����
�C autoMappingBehaviorĬ����PARTIAL�������Զ�ӳ��Ĺ��ܡ�Ψһ��Ҫ����������javaBean������һ��
�C ���autoMappingBehavior����Ϊnull���ȡ���Զ�ӳ��
�C ���ݿ��ֶ������淶��POJO���Է����շ�����������A_COLUMN?aColumn�����ǿ��Կ����Զ��շ���������ӳ�书�ܣ�mapUnderscoreToCamelCase=true��

#### 2���Զ���resultMap��ʵ�ָ߼������ӳ�䡣

#### resultMap
? constructor - ����ʵ����ʱ, ����ע���������췽����
�C idArg - ID ����; ��ǽ����Ϊ ID ���԰����������Ч��
�C arg - ע�뵽���췽����һ����ͨ���
? id �C һ�� ID ���; ��ǽ����Ϊ ID ���԰����������Ч��
? result �C ע�뵽�ֶλ� JavaBean ���Ե���ͨ���
? association �C һ�����ӵ����͹���;�������������������
�C Ƕ����ӳ�� �C ���ӳ������Ĺ���,���߲ο�һ��
? collection �C �������͵ļ�
�C Ƕ����ӳ�� �C ���ӳ������ļ�,���߲ο�һ��
? discriminator �C ʹ�ý��ֵ������ʹ���ĸ����ӳ��
�C case �C ����ĳЩֵ�Ľ��ӳ��
? Ƕ����ӳ�� �C �������ν��Ҳӳ��������,��˿��԰����ܶ���ͬ��Ԫ
��,���������Բ���һ���ⲿ�Ľ��ӳ�䡣

##### id & result
? id �� result ӳ��һ�������е�ֵ������������
(�ַ���,����,˫���ȸ�����,���ڵ�)�����Ի��ֶΡ�
![image](.img/mybatis-19.png)

##### association
? ���Ӷ���ӳ��
? POJO�е����Կ��ܻ���һ������
? ���ǿ���ʹ�����ϲ�ѯ�����Լ������Եķ�ʽ��
װ����
![image](.img/mybatis-20.png)
? ʹ��association��ǩ�������ķ�װ����

##### association-Ƕ�׽����
![image](.img/mybatis-21.png)


##### association-�ֶβ�ѯ
![image](.img/mybatis-22.png)
select������Ŀ��ķ�����ѯ��ǰ���Ե�ֵ
column����ָ���е�ֵ����Ŀ�귽��

##### association-�ֶβ�ѯ&�ӳټ���
�����ӳټ��غ����԰������
![image](.img/mybatis-23.png)
? �ɰ汾��MyBatis��Ҫ�����֧�ְ�
�C asm-3.3.1.jar
�C cglib-2.2.2.jar

##### Collection-��������&Ƕ�׽����
![image](.img/mybatis-24.png)
![image](.img/mybatis-25.png)

##### Collection-�ֲ���ѯ&�ӳټ���
![image](.img/mybatis-26.png)

##### ��չ-����ֵ��װmap����
? �ֲ���ѯ��ʱ��ͨ��columnָ��������Ӧ���е����ݴ��ݹ�ȥ��������ʱ��Ҫ���ݶ������ݡ�
? ʹ��{key1=column1,key2=column2��}����ʽ
![image](.img/mybatis-27.png)
? association����collection��ǩ��
fetchType=eager/lazy���Ը���ȫ�ֵ��ӳټ��ز��ԣ�+ָ+���������أ�eager�������ӳټ��أ�lazy��


# �塢MyBatis-��̬SQL
- ��̬ SQL��MyBatisǿ������֮һ������ļ�����ƴװSQL�Ĳ�����
- ��̬ SQL Ԫ�غ�ʹ�� JSTL ���������ƻ��� XML ���ı����������ơ�
- MyBatis ���ù���ǿ��Ļ��� OGNL �ı��ʽ���򻯲�����
  - if
  - choose (when, otherwise)
  - trim (where, set)
  - foreach

## 5.1 if
![image](.img/mybatis-28.png)

## 5.2 choose (when, otherwise)
![image](.img/mybatis-29.png)

## 5.3 trim (where, set)
### 5.3.1 where
![image](.img/mybatis-30.png)

### 5.3.2 set
![image](.img/mybatis-31.png)

### 5.3.3 trim
![image](.img/mybatis-32.png)

## 5.4 foreach
��̬ SQL ������һ�����õı�Ҫ��������Ҫ��һ�����Ͻ��б�����ͨ�����ڹ��� IN ��������ʱ��
![image](.img/mybatis-33.png)
- �������б����ϵȿɵ��������������ʱ
  - index�ǵ�ǰ�����Ĵ�����item��ֵ�Ǳ��ε�����ȡ��Ԫ��
- ��ʹ���ֵ䣨����Map.Entry����ļ��ϣ�ʱ
  - index�Ǽ���item��ֵ

## 5.5 bind
bind Ԫ�ؿ��Դ� OGNL ���ʽ�д���һ������������󶨵������ġ����磺
![image](.img/mybatis-34.png)

## 5.6 Multi-db vendor support
���� mybatis �����ļ��������� databaseIdProvider , �����ʹ�� ��_databaseId�������������Ϳ��Ը��ݲ�ͬ�����ݿ⳧�̹����ض������
![image](.img/mybatis-35.png)

OGNL�� Object Graph Navigation Language������ͼ�������ԣ�����һ��ǿ��ı��ʽ���ԣ�ͨ�������Էǳ�������������������ԡ� ���������ǵ�EL��SpEL��
![image](.img/mybatis-36.png)


# ����MyBatis-�������
MyBatis ����һ���ǳ�ǿ��Ĳ�ѯ��������,�����Էǳ���������úͶ��ơ�������Լ����������ѯЧ�ʡ�

MyBatisϵͳ��Ĭ�϶������������档

һ������Ͷ������档
- 1��Ĭ������£�ֻ��һ�����棨SqlSession����Ļ��棬Ҳ��Ϊ���ػ��棩������
- 2������������Ҫ�ֶ����������ã����ǻ���namespace����Ļ��档
- 3��Ϊ�������չ�ԡ�MyBatis�����˻���ӿ�Cache�����ǿ���ͨ��ʵ��Cache�ӿ����Զ���������档


## 6.1 һ������
һ������(local cache), �����ػ���, ������Ĭ��ΪsqlSession���� Session flush �� close ��, ��Session �е����� Cache ������ա�

==���ػ��治�ܱ��ر�==,�����Ե���clearCache()����ձ��ػ���,���߸ı仺���������.

��mybatis3.1֮��, �������ñ��ػ����������. �� mybatis.xml ������
![image](.img/mybatis-37.png)

## 6.2 һ��������ʾ&ʧЧ���
ͬһ�λỰ�ڼ�ֻҪ��ѯ�������ݶ��ᱣ���ڵ�ǰSqlSession��һ��Map�С�
? key:hashCode+��ѯ��SqlId+��д��sql��ѯ���+����

һ������ʧЧ�����������
- 1����ͬ��SqlSession��Ӧ��ͬ��һ������
- 2��ͬһ��SqlSession���ǲ�ѯ������ͬ
- 3��ͬһ��SqlSession���β�ѯ�ڼ�ִ�����κ�һ����ɾ�Ĳ���
- 4��ͬһ��SqlSession���β�ѯ�ڼ��ֶ�����˻���

## 6.3 ��������
��������(second level cache)������namespace���𻺴棬һ��namespace��Ӧһ���������档

**�������ƣ�**
1��һ���Ự����ѯһ�����ݣ�������ݻᱻ���ڵ�ǰ�Ự��һ�������У�
2������Ự�رգ�һ�������е����ݻᱻ���浽���������У��µĻỰ��ѯ��Ϣ����ն��������е����ݡ�
3��SqlSession===EmployeeMapper ===>Employee  (namespace�൱��XXXMapper)
DepartmentMapper===>Department
��ͬnamespace��ѯ�������ݻ�����Լ���Ӧ�Ļ�����(map��)


��������Ĭ�ϲ���������Ҫ�ֶ�����

MyBatis�ṩ��������Ľӿ��Լ�ʵ�֣�����ʵ��Ҫ��POJOʵ��Serializable�ӿ�
==���������� SqlSession �رջ��ύ֮��Ż���Ч==

**ʹ�ò���**
�C 1��ȫ�������ļ��п�����������
? <setting name="cacheEnabled" value="true"/>
�C 2����Ҫʹ�ö��������ӳ���ļ���ʹ��cache���û���
? <cache />
�C 3��ע�⣺POJO��Ҫʵ��Serializable�ӿ�

## 6.4 �����������
- eviction=��FIFO����������ղ��ԣ�
  -  LRU �C �������ʹ�õģ��Ƴ��ʱ�䲻��ʹ�õĶ���
  -  FIFO �C �Ƚ��ȳ�����������뻺���˳�����Ƴ����ǡ�
  -  SOFT �C �����ã��Ƴ���������������״̬�������ù���Ķ���
  -  WEAK �C �����ã����������Ƴ����������ռ���״̬�������ù���Ķ���
  -  Ĭ�ϵ��� LRU��
- flushInterval��ˢ�¼������λ����
  -  Ĭ������ǲ����ã�Ҳ����û��ˢ�¼������������������ʱˢ��
- size��������Ŀ��������
  -  �����������Դ洢���ٸ�����̫�����׵����ڴ����
- readOnly��ֻ����true/false
  -  true��ֻ�����棻������е����߷��ػ���������ͬʵ���������Щ����
     ���ܱ��޸ġ����ṩ�˺���Ҫ���������ơ�����ȫ���ٶȿ�
  -  false����д���棻�᷵�ػ������Ŀ�����ͨ�����л����������һЩ��
     ���ǰ�ȫ�����Ĭ���� false��

## 6.5 �����й�����
1��ȫ��setting��cacheEnable��
�C ���ö�������Ŀ��ء�һ������һֱ�Ǵ򿪵ġ�

2��select��ǩ��useCache���ԣ�
�C �������select�Ƿ�ʹ�ö������档һ������һֱ��ʹ�õ�

3��sql��ǩ��flushCache���ԣ�
�C ��ɾ��Ĭ��flushCache=true��sqlִ���Ժ󣬻�ͬʱ���һ���Ͷ������档
��ѯĬ��flushCache=false��

4��sqlSession.clearCache()��
�C ֻ���������һ�����档

5������ĳһ�������� (һ������Session/��������
Namespaces) ������ C/U/D ������Ĭ�ϸ������������� select �еĻ��潫��clear��


# �ߡ�MyBatis-Spring����
1���鿴��ͬMyBatis�汾����Springʱʹ�õ��������
http://www.mybatis.org/spring/

2���������������
https://github.com/mybatis/spring/releases

3���ٷ�����ʾ����jpetstore
https://github.com/mybatis/jpetstore-6

���Ϲؼ����ã�
```
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
<!-- ָ��mybatisȫ�������ļ�λ�� -->
<property name="configLocation" value="classpath:mybatis/mybatis-config.xml"></property>
<!--ָ������Դ -->
<property name="dataSource" ref="dataSource"></property>
<!--mapperLocations������sqlӳ���ļ����ڵ�λ�� -->
<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"></property>
<!--typeAliasesPackage��������������-->
<property name="typeAliasesPackage" value="com.atguigu.bean"></property>
</bean>
<!--�Զ���ɨ�����е�mapper��ʵ�ֲ����뵽ioc������ -->
<bean id="configure" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<!�C basePackage:ָ���������е�mapper�ӿ�ʵ���Զ�ɨ�貢���뵽ioc������ -->
<property name="basePackage" value="com.atguigu.dao"></property>
</bean>

```


# �ˡ�MyBatis-���򹤳�
MyBatis Generator��

? ���MBG����һ��ר��ΪMyBatis���ʹ���߶��ƵĴ��������������Կ��ٵĸ��ݱ����ɶ�Ӧ��ӳ���ļ����ӿڣ��Լ�bean�ࡣ֧�ֻ�������ɾ�Ĳ飬�Լ�QBC����������ѯ�����Ǳ����ӡ��洢���̵���Щ����sql�Ķ�����Ҫ�����ֹ���д

? �ٷ��ĵ���ַ
http://www.mybatis.org/generator/

? �ٷ����̵�ַ
https://github.com/mybatis/generator/releases

## 8.1 MBGʹ��
ʹ�ò��裺
�C 1����дMBG�������ļ�����Ҫ�������ã�
1��jdbcConnection�������ݿ�������Ϣ
2��javaModelGenerator����javaBean�����ɲ���
3��sqlMapGenerator ����sqlӳ���ļ����ɲ���
4��javaClientGenerator����Mapper�ӿڵ����ɲ���
5��table ����Ҫ������������ݱ�
tableName������
domainObjectName����Ӧ��javaBean��
�C 2�����д������������ɴ���

ע�⣺
Context��ǩ
targetRuntime=��MyBatis3���������ɴ���������ɾ�Ĳ�
targetRuntime=��MyBatis3Simple���������ɻ�������ɾ�Ĳ�
����ٴ����ɣ����齫֮ǰ���ɵ�����ɾ��������xml���׷�����ݳ��ֵ���
�⡣

## 8.2 MBG�����ļ�

```
<generatorConfiguration>
<context id="DB2Tables" targetRuntime="MyBatis3">
//���ݿ�������Ϣ����
<jdbcConnection driverClass="com.mysql.jdbc.Driver"
connectionURL="jdbc:mysql://localhost:3306/bookstore0629"
userId="root" password="123456">
</jdbcConnection>
//javaBean�����ɲ���
<javaModelGenerator targetPackage="com.atguigu.bean" targetProject=".\src">
<property name="enableSubPackages" value="true" />
<property name="trimStrings" value="true" />
</javaModelGenerator>
//ӳ���ļ������ɲ���
<sqlMapGenerator targetPackage="mybatis.mapper" targetProject=".\conf">
<property name="enableSubPackages" value="true" />
</sqlMapGenerator>
//dao�ӿ�java�ļ������ɲ���
<javaClientGenerator type="XMLMAPPER" targetPackage="com.atguigu.dao" 
targetProject=".\src">
<property name="enableSubPackages" value="true" />
</javaClientGenerator>
//���ݱ���javaBean��ӳ��
<table tableName="books" domainObjectName="Book"></table>
</context>
</generatorConfiguration>

```


# �š�MyBatis-����ԭ��
![image](.img/mybatis-38.png)

# ʮ��MyBatis-�������

