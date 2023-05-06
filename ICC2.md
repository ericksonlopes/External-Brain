Tabela ACE 1 e 2 n junta
2 = 555
3 = 999

PHARMATYPE(3, 4 ,10) join


se for o primeiro  = T0
se hospital_registry for igual  E hospitalizacao_arm_1 = Ta

1 = Ta
2 = Tb
3 = Tc
...


hospitalizacao_arm_1 = T0
fu_2_30d_arm_1 = T30

fu_2_(Total de dias)_arm_1 = T(Total de dias)


```
import requests data = { 'token': 'D37063A905B619510904523CCB559B57', 'content': 'record', 'format': 'csv', 'returnFormat': 'json' } r = requests.post('[https://servicos3.hcor.com.br/redcap/api/',data=data](https://servicos3.hcor.com.br/redcap/api/',data=data)) print('HTTP Status: ' + str(r.status_code)) print(r.text)
````




