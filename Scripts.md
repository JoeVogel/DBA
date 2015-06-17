###Criar tablespace:
CREATE TABLESPACE data01 
	DATAFILE 'ORACLE_DATA/data01.dbf' SIZE 10M;
    
    data01 						= Estrutura lógica
	'ORACLE_DATA/data01.dbf' 	= Estrutura física    

CREATE TABLESPACE data01
	DATAFILE 'ORACLE_DATA/data01.dbf' SIZE 10M
    AUTOEXTEND ON NEXT 10M MAXSIZE 500M;

###Alterar tablespace:
ALTER TABLESPACE data01 READ ONLY;




