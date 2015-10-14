##DBA 1

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
    
###Movimentação de arquivos de dados da tablespace

tablespace deve estar off-line => alter tablespace data01 offline;

Os arquivos de dados de destino devem existir, ou seja, aqui apenas mudamos o ponteiro do oracle. Os arquivos devem ser movidos manualmente via sistema operacional (estando na pasta destino, cp $ORACLE_DATA/data01.dbf .) Este ponto no final faz parte do comando

ALTER TABLESPACE userdata RENAME
	DATAFILE '$ORACLE_DATA/data01.dbf'
    TO '$HOME/data01.dbf';
    
passar tablespace para online => alter tablespace data01 online;


###Eliminando tablespaces

Não será possivel eliminar um tablespace se ele:
	- Estiver no tablespace SYSTEM
    - Tiver segmentos ativos
    
INCLUDING CONTENTS elimina os segmentos
INCLUDING CONTENTS AND DATAFILES deleta arquivos de dados
CASCADE CONSTRAINTS elimina todas as 

DROP TABLESPACE ronly INCLUDING CONTENTS AND DATAFILES;

###ELiminando tabelas

DROP TABLE hr.departamentos
CASCADE CONSTRAINTS;

##DBA 2

###Create user

CREATE USER aaron
IDENTIFIED BY soccer
DEFAULT TABLESPACE data01
TEMPORARY TABLESPACE temp
QUOTA 15M ON data01
QUOTA 10M ON users
PASSWORD EXPIRE;

###Alter USER QUOTA
ALTER USER aaron
QUOTA 2M ON data01;

###Visões usuários
DESC DBA_USERS;
SELECT * FROM DBA_USERS;

DESC DBA_TS_QUOTAS;
SELECT * FROM DBA_TS_QUOTAS;

###Exibir cotas
SELECT tablespace_name, sum(bytes)/1024 quota
FROM dba_ts_quotas
WHERE username = 'AARON'
GROUP BY tablespace_name;

###Auditoria
SHOW PARAMETER AUDIT

Parâmetro: AUDIT_TRAIL

NONE = auditoria desativada <br />
DB   = auditoria ativa e direciona todos os registros para a trilha do BD <br />
SO   = auditoria ativa e direciona os registros para um log do S.O.
