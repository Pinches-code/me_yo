## Тестовые задания компаний для приема на работу из открытых источиков
    
Для отработки практических навыков я регулярно выполняю тестовые задания, размещенные компаниями в открытых источниках в рамках процессов найма. Это позволяет мне погружаться в реальные кейсы, тренировать решение задач в условиях, приближенных к рабочим, и совершенствовать владение инструментами (Python, SQL, Git, аналитические системы).       
Все выполненные проекты я оформляю в виде структурированного кода с комментариями и прикрепляю к GitHub-репозиторию, чтобы наглядно демонстрировать уровень компетенций, подход к оптимизации решений и умение работать с требованиями. Такой метод помогает не только закрепить теорию, но и создать портфолио, отражающее мою готовность к решению производственных задач.

## Amazon SQL and Python Task | Junior | jan/2025
**Задача 1**     
Дана таблица Orders, Необходимо написать запрос для команды маркетинга, который выявит всех киентов, кто купил iPhone, но не купил Airpods, чтобы отправить им маркетинговое предложение.      

**Решение:**         
WITH order_summary AS (    
    SELECT      
        Order_id,     
        Customer_name,      
        TO_DATE(     
            REGEXP_REPLACE(Order_day, '(st|nd|rd|th)', '', 'g'),      
            'DD-Mon-YY'     
        ) AS order_date,               
        MAX(CASE WHEN Prod_Name = 'iPhone' THEN 1 ELSE 0 END) AS has_iphone,                
        MAX(CASE WHEN Prod_Name = 'Airpods' THEN 1 ELSE 0 END) AS has_airpods                            
    FROM orders               
    GROUP BY Order_id, Customer_name, Order_day                       
)             
SELECT                    
    EXTRACT(MONTH FROM order_date) AS month,            
    COUNT(DISTINCT Customer_name) AS customer_count                      
FROM order_summary       
WHERE has_iphone = 1 AND has_airpods = 0            
GROUP BY EXTRACT(MONTH FROM order_date)         
ORDER BY month;            

**Задача 2**      
Дана таблица с историей подписки. Есть начало и конече периода подписки и статус. Нужно получить результат, как sample output . Условие для типа подписки так же дано.     
   
**Решение:**     
WITH subscription_changes AS (                      
    SELECT                 
        customer_id,              
        membership_start_date AS change_date,             
        membership_status AS current_status,              
        LAG(membership_status) OVER (             
            PARTITION BY customer_id             
            ORDER BY membership_start_date           
        ) AS previous_status                   
    FROM subscriptions              
)          
SELECT              
    customer_id,             
    change_date,              
    CASE                       
        WHEN previous_status IS NULL THEN 'WarmStart'            
        WHEN previous_status = 'Free' AND current_status = 'Paid' THEN 'Convert'            
        WHEN previous_status = 'Paid' AND current_status = 'Free' THEN 'ReverseConvert'                        
        WHEN (previous_status = 'Paid' OR previous_status = 'Free') AND current_status = 'Non-member' THEN 'Cancel'               
        WHEN previous_status = 'Non-member' AND current_status = 'Paid' THEN 'ColdStart'                         
        WHEN previous_status = 'Non-member' AND current_status = 'Free' THEN 'WarmStart'       
        WHEN previous_status = current_status THEN 'Renewal'          
        ELSE 'Unknown'      
    END AS event              
FROM subscription_changes            
ORDER BY customer_id, change_date;                      

**Задача 3**  
Дан LIST, нужно его сохранить задам наперед. 

**Решение:**   
Использование срезов (Slicing)      
def reverse(lst):      
    return lst[::-1]     

Проверка работы:                

print(reverse(['A', 'B', 'C', 'D', 'E']))  # ['E', 'D', 'C', 'B', 'A']    
