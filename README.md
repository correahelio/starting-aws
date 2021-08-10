Resumindo o que vamos fazer nesse primeiro passo:
- Entrar na nossa conta da AWS.
- Vamos configurar alguns alarmes de custo.
- Vamos preparar a nossa conta com um mínimo de segurança.

O que você precisa ter de ambiente na sua máquina local para começarmos ?
- Nesse primeiro momento é recomendado que você tenha Git instalado.
- Legal você ter um editor de texto (VSCode por exemplo vai ser muito útil).

Chega de falar, vamos fazer.

1) Acessando a AWS pela "primeira" vez:
- Bom, se vc chegou aqui eu espero que vc já tenha uma conta na AWS, certo ?
- Se vc tem, faça login nela.
- Legal, uma vez que vc fez login vamos falar do primeiro conceito interessante, que é SEGURANÇA BÁSICA.


Configurando IAM:
- Por padrão, seu usuário que é dono da conta tem acesso root. Já vamos aproveitar e evitar desconforto no futuro.
- Abra o serviço IAM.
- Apague a chave do root da sua conta.
	Link de referência da AWS: https://docs.aws.amazon.com/pt_br/IAM/latest/UserGuide/id_root-user.html#id_root-user_manage_delete-key
	
	![image](https://user-images.githubusercontent.com/22084402/128793855-7fdbeb98-0a2b-44f2-9160-426edd666fde.png)


- Vamos configurar um outro fator de autenticação no seu usuário root que é o MFA (Activate multi-factor authentication). 
- 
	![image](https://user-images.githubusercontent.com/22084402/128793865-af757197-a310-4b1c-8461-647bd119d576.png)
	
- Eu utilizo o Google Authenticator no Android. Pode utilzar outro se preferir.
	Obs.: Reparei que após algum tempo sem usar o Google Authenticator ele acaba se perdendo e tirando a sincronia do MFA.
	- Com isso, após fazer login e errar o MFA algumas vezes, ele vai pedir para vc digitar 2x para que o mesmo seja sincronizado novamente.


- Vamos criar um usuário para utilizarmos:
- Abra a opção "Groups" (menu da esquerda) e vamos criar um Group. Eu vou chamá-lo de "Administrators" e vou colocar a Policy de "AdministratorAccess".


	![image](https://user-images.githubusercontent.com/22084402/128793913-1db575e7-55f0-43ff-ab1b-dcecb36e4dba.png)

- Agora que temos um Group, vamos criar um usuário. 
- Abra a opção "Users" (menu da esquerda) e crie um usuário. 
- Vincule esse usuário ao grupo que criamos anteriormente que é o grupo "Administrators".

	![image](https://user-images.githubusercontent.com/22084402/128793924-36e23463-9152-41d4-9ad7-bf556d1f6489.png)
	
- Aproveita e já ativa MFA também para seu novo usuário.

	![image](https://user-images.githubusercontent.com/22084402/128793931-cd4a82ae-9a92-4700-969b-b1148ddcdac1.png)

Bom, nesse momento você já passou pela segurança básica. Recapitulando:
- Apagamos a chave do root.
- Configuramos MFA no usuário root.
- Criamos usuário com perfil ADM e também com MFA.

O que falta: é CUSTO.
- Aproveite que ainda está logado com o root da conta e vamos configurar alguns alarmes de custo, para evitarmos deixar algum ligado por muito tempo.
- Abra o serviço CloudWatch.
- No menu da esquerda abra a opção de Alarms e crie um Alarm.
- Utilize a métrica "EstimatedCharge".

![image](https://user-images.githubusercontent.com/22084402/128793945-1d44c8cf-d808-413e-b988-1f595c29f665.png)

	- Para isso, selecione uma métrica de "Billing" > "Total EstimatedCharge" > Selecione a opção em USD EstimatedCharge.
	
	- Reparem que a minha métrica está com o tempo default, ou seja, ela vai rodar a cada 6 horas e verificar se está em condição de ALARME.
		- No meu uso da AWS isso me atende. Caso necessário, customize a métrica do seu jeito.
	- Na próxima página será necessário configurar um tópico SNS. Ele será responsável por realizar o disparo.
	- Caso vc precise sair da tela e criar um tópico, peço desculpas, mas eu já estava escrevendo essse ponto e não vou parar. Boa sorte pra vc que vai ter que refazer esse passo! (=
	
	
	



- Bom, após configurar o disparo do SNS o seu alarme vai fica em status "OK".
- Caso atinga os datapoints configurados um email será disparado para vc.
- No meu caso, eu tenho 3 alarmes de custo (5, 7 e 10 dólares). Segue a configuração dos meus alarmes.

![image](https://user-images.githubusercontent.com/22084402/128794021-dbe456fc-d355-4abc-bd46-9d94ee627bca.png)

	

Configurando o tópico SNS para disparar seu alarme:
Abra o serviço de SNS.
- Crie um tópico. No meu caso chamei-o de "NotifyMe".
- Entre no tópico e crie um "Subscription" do protocolo "Email".
- Digite seu email.
- Vc vai receber um email de confirmação. Após confirmar o email vc deve ficar com status OK no tópico.

Para finalizarmos:
- Teste o logout / login da sua conta root com MFA.
- Teste login do novo usuário com MFA.
- A partir de agora não devemos mais voltar na conta root. Devemos utilizar apenas nosso usuário com criado.
- Faça login com o outro usuário.
	- Escolha a opção "Sign in to a different account".


	![image](https://user-images.githubusercontent.com/22084402/128794038-2809c39d-6f7f-4c27-abd0-758bfe00a9a3.png)
	
	![image](https://user-images.githubusercontent.com/22084402/128794045-ec90130a-068b-475a-816f-bb4cf0657d74.png)

Obs.: O alarme em questão poderia ter sido criado com o novo usuário. Apenas aproveitei o 1o login do root.
Obs.: Com o usuário criado vc não consegue ver o Billing, ou seja, para ver seus gastos tem que acessar a conta do root ou dar a permissão necessária.


![image](https://user-images.githubusercontent.com/22084402/128794422-8a2e9112-ed49-4a43-b0b3-efc18f96f8ad.png)


Se vc chegou até aqui vc tem:
- Alarme de custo na sua conta.
- Usuário root sem chave e com MFA.
- Usuário de adm para brincar com MFA.
