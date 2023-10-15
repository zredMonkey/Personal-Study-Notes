# һ��ע�����
@Scheduledע����Spring Boot�ṩ�����ڶ�ʱ������Ƶ�ע�⣬��Ҫ���ڿ���������ĳ��ָ��ʱ��ִ�У�����ÿ��һ��ʱ��ִ�С�

#  ����ע�����
## 2.1. �������


| ���� | ˵�� | ʾ�� |
| --- | --- | --- |
|cron |	 ����ִ�е�cron���ʽ |	������ |
|zone |	cron���ʱ����ʹ�õ�ʱ����Ĭ��Ϊ�������ı���ʱ����ʹ��java.util.TimeZone#getTimeZone(String)�������� |	GMT-8:00 |
| fixedRate |	�̶����ʡ���һ������ִ�п�ʼ����һ��ִ�п�ʼ�ļ��ʱ��̶�����λΪms�����ڵ�������ִ��ʱ,��һ������δִ�����,�����worker����,�ȴ���һ��ִ����ɺ�����ִ����һ������|	1000
| fixedRateString |	��fixedRateһ�£�ֻ�Ǽ��ʱ��ʹ��java.time.Duration#parse���� |	1000��PT1S |
| fixedDelay |	 �̶��ӳ١���һ������ִ�н�������һ��ִ�п�ʼ�ļ��ʱ��̶�����λΪms��|	1000 |
| fixedDelayString | ��fixedDelayһ�£�ֻ�Ǽ��ʱ��ʹ��java.time.Duration#parse���� |	1000��PT1S |
| initialDelay |	�״��ӳٶ೤ʱ���ִ�У���λms��֮����fixedRate��fixedRateString��fixedDelay��fixedDelayStringָ���Ĺ���ִ�У���Ҫָ������һ������ע�⣺���ܺ�cronһ��ʹ��  |	1000 |
| initialDelayString |	 ��initialDelay һ�£�ֻ�Ǽ��ʱ��ʹ��java.time.Duration#parse����  |	 1000��PT1S |

## 2.2. ��Ҫ����˵��
ע�⣬@Scheduled��Ҫ���@EnableSchedulingʹ�á�ʹ��ʱ����@Scheduledע����ڴ���ʱ�ķ������Ϸ�����@EnableScheduling������Ŀ�������������Ϸ���@Scheduled��Ҫ����������ִ��ʱ��ķ�ʽ��cron��fixedRate��fixedDelay��

### 2.2.1. cron���ʽ
cron��@Scheduled��һ����������һ���ַ������Կո������

```
cron���ʽ��ʽ��
 
@Scheduled(cron = "{����} {����} {Сʱ} {����} {�·�} {����}")
 
cron���ʽʾ����
 
@Scheduled(cron = "0 00 07 * * *")
```
cron���ʽ�ɷ�Ϊ6��7��ռλ��������spring�Դ��Ķ�ʱ�����У�cronֻ֧��6��������������ο�2.3��Դ�룩����ʹ��7�������ͻᱨ��ʾ�����£�

```
@Scheduled(cron = "0/3 * * * * ? 2022-2023")
public void test(){
 
    logger.info("������ԣ�");
 
}
 
 
org.springframework.beans.factory.BeanCreationException: 
Error creating bean with name '����' defined in file 
[�����class�ļ�·��]: Initialization of bean failed; 
 
nested exception is java.lang.IllegalStateException: 
Encountered invalid @Scheduled method 'test': Cron expression 
must consist of 6 fields (found 7 in "0/3 * * * * ? 2022-2023")
```
���������ܣ�

| ��λ | ����ֵ | ����ͨ��� |
| --- | --- | --- |
| ���� | 	0-59 | 	,  -  *  /  | 
| ���� | 	0-59 | 	,  -  *  / | 
| Сʱ | 	0-23 |  	,  -  *  /  | 
| ���� | 	1-31 | 	,  -  *  /  ?  L  W | 
| �·� | 	1-12��JAN-DEC(��Сд����) | 	,  -  *  /  ? | 
| ���� | 	1-7��SUN-SAT(��Сд����) | ע��������Ϊÿ�ܵ�һ�죬����1-7��ʾ��ĩ������ ,  -  *  /  ?  L  # | 

cron���ʽ��ռλ�����ͣ�

```
{����}{����}{Сʱ}{����}{����} ==> ������Ϊ��ֵ����ֵ���Ϸ������������׳�SchedulerException�쳣
 
��/������������(step)����/��ǰ���ֵ�����ʼֵ(""��ͬ"0")�������ֵ����ƫ����������
 
"0/20"����"/20"�����0���ӿ�ʼ��ÿ��20���Ӵ���1�Σ�����0�봥��1�Σ���20�봥��1�Σ���40�봥��1�Σ�
 
"5/20"�����5�봥��1�Σ���25�봥��1�Σ���45�봥��1�Σ�
 
"10-45/20"������[10,45]�ڲ���20�����е�ʱ��㴥��������10�봥��1�Σ���30�봥��1��
 
```
### 2.2.2. cronͨ���

| ���� | ���� |
| --- | --- |
|* |	����ֵ,�����ֶ��ϱ�ʾÿ��ִ��,�����ֶ��ϱ�ʾÿ��ִ�� |
|��	| ��ָ��ֵ������Ҫ���ĵ�ǰָ�����ֶε�ֵ������ÿ�춼ִ�е�����Ҫ�����ܼ��Ϳ��԰��ܵ��ֶ���Ϊ? |
|- |	������߷�Χ,�����0-2 ,��ʾ0�롢1�롢2�붼�ᴥ�� |
|, |	ָ��ֵ��������0�롢20�롢25�봥��,���԰�����ֶ���Ϊ0,20,25 |
|/ |	��������,��������ֶ�����0/3 ,��ʾ�ӵ�0�뿪ʼ,ÿ��3�봥�� |
|L |	���ֻ���������ֶλ����ֶ���,�����ֶ���ʹ��L��ʾ�������- -��,�����ֶ���ʹ��3L��ʾ�������һ������ |
|W |	ֻ�����������ֶ���,��ʾ��������ĸ��յĹ�����,������ָ������һ������ |
|# |	ֻ���������ֶ��ϣ���ʾÿ�µĵڼ����ܼ�����2#3 , ÿ�µĵ�3���ܶ� |


### 2.2.3. cron���ʽʾ��
@Scheduled(cron = "* * * * * *")ʾ����*ÿ��ִ��


@Scheduled(cron = "10-45/20 * * * * *")ʾ����-��/ռλ�������ʹ��


@Scheduled(cron = "*/5 * * * * *")ʾ����*��/��ʹ�ã�����������ÿ5��ִ��һ��

@Scheduled(cron = "25/5 * * * * *")ʾ����������ʾ���Աȣ���25����ÿ5��ִ��һ��

����ʾ����

```
��30 * * * * ?�� ÿ����Ӵ������� 
 
��30 10 * * * ?�� ÿСʱ��10��30�봥������ 
 
��30 10 1 * * ?�� ÿ��1��10��30�봥������ 
 
��30 10 1 20 * ?�� ÿ��20��1��10��30�봥������ 
 
��30 10 1 20 10 ? *�� ÿ��10��20��1��10��30�봥������ 
 
��30 10 1 20 10 ? 2011�� 2011��10��20��1��10��30�봥������ 
 
��30 10 1 ? 10 * 2011�� 2011��10��ÿ��1��10��30�봥������ 
 
��30 10 1 ? 10 SUN 2011�� 2011��10��ÿ����1��10��30�봥������ 
 
��15,30,45 * * * * ?�� ÿ15�룬30�룬45��ʱ�������� 
 
��15-45 * * * * ?�� 15��45���ڣ�ÿ�붼�������� 
 
��15/5 * * * * ?�� ÿ���ӵ�ÿ15�뿪ʼ������ÿ��5�봥��һ�� 
 
��15-30/5 * * * * ?�� ÿ���ӵ�15�뵽30��֮�俪ʼ������ÿ��5�봥��һ�� 
 
��0 0/3 * * * ?�� ÿСʱ�ĵ�0��0�뿪ʼ��ÿ�����Ӵ���һ�� 
 
��0 15 10 ? * MON-FRI�� ����һ���������10��15��0�봥������ 
 
��0 15 10 L * ?�� ÿ�������һ���10��15��0�봥������ 
 
��0 15 10 LW * ?�� ÿ�������һ�������յ�10��15��0�봥������ 
 
��0 15 10 ? * 5L�� ÿ�������һ�������ĵ�10��15��0�봥������ 
 
��0 15 10 ? * 5#3�� ÿ���µ����ܵ������ĵ�10��15��0�봥������
```

### 2.2.4. cron���ʽ������������

```
cron���ʽ��ʽ���ڴˣ����ʽ�Կո��Ϊ6��7����
 
@Scheduled(cron = "{����} {����} {Сʱ} {����} {�·�} {����} {���(��Ϊ��)}")
 
{���} ==> ����ֵ��Χ: 1970~2099 ,����Ϊ�գ���ֵ���Ϸ������������׳�SchedulerException�쳣
 
ע�⣺����{����}��{����}����ʹ�á�?����ʵ�ֻ��⣬������������Ϣ֮�⣬����ռλ����Ҫ���о����ʱ�京�壬��������ϵΪ����->��->����(����)->Сʱ->����->����
```

## 2.3.Դ��

```
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Repeatable(Schedules.class)
public @interface Scheduled {
 
	/**
	 * A special cron expression value that indicates a disabled trigger: {@value}.
	 * һ�������cron���ʽֵ��ָʾ���õĴ���������@value����
	 * <p>This is primarily meant for use with <code>${...}</code> placeholders,
	 * allowing for external disabling of corresponding scheduled methods.
	 * @since 5.1
	 * @see ScheduledTaskRegistrar#CRON_DISABLED
	 */
	String CRON_DISABLED = ScheduledTaskRegistrar.CRON_DISABLED;
 
 
	/**
	 * A cron-like expression, extending the usual UN*X definition to include triggers
	 * on the second, minute, hour, day of month, month, and day of week.
	 * ����cron�ı��ʽ����ͨ����UN*X������չΪ�������������롢���ӡ�Сʱ���졢�·ݺ����ڼ���
	 * 
	 * <p>For example, {@code "0 * * * * MON-FRI"} means once per minute on weekdays
	 * (at the top of the minute - the 0th second).
	 * <p>The fields read from left to right are interpreted as follows.
	 * �����Ҷ�ȡ���ֶν������¡�
	 * <ul>
	 * <li>second</li>
	 * <li>minute</li>
	 * <li>hour</li>
	 * <li>day of month</li>
	 * <li>month</li>
	 * <li>day of week</li>
	 * </ul>
	 * <p>The special value {@link #CRON_DISABLED "-"} indicates a disabled cron
	 * trigger, primarily meant for externally specified values resolved by a
	 * <code>${...}</code> placeholder.
	 * @return an expression that can be parsed to a cron schedule
	 * @see org.springframework.scheduling.support.CronExpression#parse(String)
	 */
	String cron() default "";
 
	/**
	 * A time zone for which the cron expression will be resolved. 
	 * ������cron���ʽ��ʱ����
	 * By default, this attribute is the empty String (i.e. the server's local time zone will be used).
	 * Ĭ������£�������Ϊ���ַ���������ʹ�÷������ı���ʱ������
	 * @return a zone id accepted by {@link java.util.TimeZone#getTimeZone(String)},
	 * or an empty String to indicate the server's default time zone
	 * @since 4.0
	 * @see org.springframework.scheduling.support.CronTrigger#CronTrigger(String, java.util.TimeZone)
	 * @see java.util.TimeZone
	 */
	String zone() default "";
 
	/**
	 * Execute the annotated method with a fixed period in milliseconds between the
	 * end of the last invocation and the start of the next.
	 * ����һ�ε��ý�������һ�ε��ÿ�ʼ֮���Ժ���Ϊ��λ�Ĺ̶�������ִ�д�ע�͵ķ�����
	 * 
	 * @return the delay in milliseconds
	 */
	long fixedDelay() default -1;
 
	/**
	 * Execute the annotated method with a fixed period in milliseconds between the
	 * end of the last invocation and the start of the next.
	 * ����һ�ε��ý�������һ�ε��ÿ�ʼ֮���Ժ���Ϊ��λ�Ĺ̶�������ִ�д�ע�͵ķ�����
	 * 
	 * @return the delay in milliseconds as a String value, e.g. a placeholder
	 * or a {@link java.time.Duration#parse java.time.Duration} compliant value
	 * @since 3.2.2
	 */
	String fixedDelayString() default "";
 
	/**
	 * Execute the annotated method with a fixed period in milliseconds between
	 * invocations.
	 * �����ε���֮���Ժ���Ϊ��λִ�д�ע�͵ķ�����
	 *
	 * @return the period in milliseconds
	 */
	long fixedRate() default -1;
 
	/**
	 * Execute the annotated method with a fixed period in milliseconds between
	 * invocations.
	 * �����ε���֮���Ժ���Ϊ��λִ�д�ע�͵ķ�����
	 *
	 * @return the period in milliseconds as a String value, e.g. a placeholder
	 * or a {@link java.time.Duration#parse java.time.Duration} compliant value
	 * @since 3.2.2
	 */
	String fixedRateString() default "";
 
	/**
	 * Number of milliseconds to delay before the first execution of a
	 * {@link #fixedRate} or {@link #fixedDelay} task.
	 * �״�ִ�У�@link#fixedRate�����@link#fixedDelay������֮ǰҪ�ӳٵĺ�������
	 *
	 * @return the initial delay in milliseconds
	 * @since 3.2
	 */
	long initialDelay() default -1;
 
	/**
	 * Number of milliseconds to delay before the first execution of a
	 * {@link #fixedRate} or {@link #fixedDelay} task.
	 * �״�ִ�У�@link#fixedRate�����@link#fixedDelay������֮ǰҪ�ӳٵĺ�������
	 *
	 * @return the initial delay in milliseconds as a String value, e.g. a placeholder
	 * or a {@link java.time.Duration#parse java.time.Duration} compliant value
	 * @since 3.2.2
	 */
	String initialDelayString() default "";
 
}
```

# ����cron����
��[����Cron���ʽ������](https://cron.qqe2.com/)��������Cron���ʽ��ͬʱ��Ҳ���Է�����������һЩ����Ա���õĹ��ߡ�