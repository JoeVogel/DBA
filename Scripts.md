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


###Adicionar arquivo ao tablespace

ALTER TABLESPACE data01
	ADD DATAFILE '$ORACLE_DATA/data01_01.dbf' SIZE 5M;


###Alterar base de dados

ALTER DATABASE
	DATAFILE '/u03/oradata/userdata02.dbf'
    RESIZE 200M;	//Aumenta o tamanho alocado para o database
    
###Movimentação de arquivos de dados

tablespace deve estar off-line => alter tablespace data01 offline;
Os arquivos de dados de destino devem existir

ALTER TABLESPACE userdata RENAME
	DATAFILE '$ORACLE_DATA/data01.dbf'
    TO '$HOME/data01.dbf';
    
passar tablespace para online => alter tablespace data01 online;




