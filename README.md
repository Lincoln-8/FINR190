# FINR190 - Protheus
Relatório relação de baixas(FINR190) com resolução paliativa.

--------------------------------------------------------------------------------------------------------------------------------------------
Descrição:

Recentemente identifiquei uma ocorrência no relatório padrão do protheus de relação de baixas(FINR190.PRX - Data: 12/01/2022), em que, 
em um determinado momento o relatório padrão entra em laço de repetição infinito(While), ocasionando travamento para os usuários do sistema
e consumindo os recursos do servidor de CPU, memória etc. Após debugar o fonte padrão identifiquei que no momento da execução da 
linha 2161: NEWSE5->(DbSkip()) ao invés dele posicionar no próximo registro ele volta pra o anterior. 

Então abri um chamado com a TOTVS sugerindo que talvez esse erro seja uma má performance entre o banco de dados Oracle e a Classe
FWTemporaryTable, provavelmente algo relacionado a LIB, como paliativo criei um relatório novo de relação de baixas(FINR190C.PRW)
onde mantenho as mesmas regras de negócio do relatório padrão, adicionando apenas 7 linhas a mais no código, substituindo apenas a classe 
FWTemporaryTable pela função MPSysOpenQuery utilizando o mesmo ALIAS NEWSE5, após isso os usuários passaram a conseguir gerar o relatório..
--------------------------------------------------------------------------------------------------------------------------------------------

--------------------------------------------------------------------------------------------------------------------------------------------
Chamado TOTVS:

Solicitação #13723076
FINR190 - Travamento, em looping - SE5RECNO 127047252, volta 127043769 dbselectarea(NEWSE5")
--------------------------------------------------------------------------------------------------------------------------------------------

--------------------------------------------------------------------------------------------------------------------------------------------
Descrições dos arquivos:

Includes padrões necessárias: 
dbstruct.ch e finr190.ch

Perguntas necessárias: 
SX1_FIN190C.rar

Relatório padrão com mal comportamento no BD Oracle: 
FINR190.PRX

Relatório customizado com uma correção paliativa: 
FINR190C.PRW

OBS: A única diferença entre os fontes FINR190.PRX e o FINR190C.PRW é que no fonte customizado existe o seguinte código a mais 
de forma que a alteração realizada no relatório é mínima garantindo toda regra de negócio do relatório padrão, segue:

NEWSE5->(dbCloseArea())                              // destroi o alias NEWSE5 que faz referência a classe FWTemporaryTable 
__cQry := " SELECT * FROM "+cTable                   // varíavel com a query a realizar lendo todos os registros e campos criados pela classe FWTemporaryTable 
MPSysOpenQuery(__cQry, "NEWSE5", U_F190FieldsC())    // abre a query

DbSelectArea("NEWSE5")                               // garante abertura do alias NEWSE5 
DbGoTop()                                            // posiciona no primeiro registro do alias NEWSE5
--------------------------------------------------------------------------------------------------------------------------------------------
