##DBA 1

###Conectar com sys
connect sys as sysdba  
passwd = manager

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

alter system set audit_trail = 'DB' scope = spfile;

Após mudar o parametro, logar como SYS (primeira linha mostra como faz) e faz:  
SHUTDOWN IMMEDIATE;
STARTUP;

###Fazer auditoria em uma tabela

AUDIT SELECT ON scott.emp BY ACCESS WHENEVER SUCCESSFUL

SELECT username, obj_name, action_name, priv_used FROM sys.dba_audit_object WHERE owner = 'SCOTT' AND obj_name = 'EMP';

###Remover auditoria
NOAUDIT SELECT ON scott.emp;

###Ver sessões ativas
SELECT username, sid, serial# from V$session;

###Matar sessão
ALTER SYSTEM KILL SESSION "SID, SERIAL#";

##Backup e Recovery

connect system/manager as sysdba

No terminal do SO:

rman target /

RMAN> startup

RMAN> run { 
allocate channel t1 type dysk format '/home/oracle/BKP_CF_28102015.rman;
backup current controlfile tag BKP_CF;
release t1;
}

Edita o arquivo controlfile.dbf na pasta $ORACLE_HOME com o banco desativado

Tenta subir o banco. Não vai funcionar

rman target /

RMAN> startup nomount;

RMAN> restore controlfile from '/home/oracle/BKP_CF_28102015.rman';

###Detalhar ações de uma query

explain plan for
select * from scott.emp
where job like 'M%';

select * from table(DBMS_XPLAN.DISPLAY);

###Ligar autotrace (ao fazer qualquer consulta ele já fará automaticamente o display do XPLAIN)

set autotrace on;

###Desligar autotrace

set autotrace off;

###Exemplo de consulta para usar no trace

select ename, dname, sal, loc from scott.emp, scott.dept;

select ename, dname, sal, loc from scott.emp e, scott.dept d where e.depno = d.deptno;
