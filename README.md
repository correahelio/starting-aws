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
	- Imagem de referência no documento: 1.1

- Vamos configurar um outro fator de autenticação no seu usuário root que é o MFA (Activate multi-factor authentication). 
	- Imagem de referência: 1.2
- Eu utilizo o Google Authenticator no Android. Pode utilzar outro se preferir.
	Obs.: Reparei que após algum tempo sem usar o Google Authenticator ele acaba se perdendo e tirando a sincronia do MFA.
	- Com isso, após fazer login e errar o MFA algumas vezes, ele vai pedir para vc digitar 2x para que o mesmo seja sincronizado novamente.


- Vamos criar um usuário para utilizarmos:
- Abra a opção "Groups" (menu da esquerda) e vamos criar um Group. Eu vou chamá-lo de "Administrators" e vou colocar a Policy de "AdministratorAccess".
	- Imagem referência: 1.3
- Agora que temos um Group, vamos criar um usuário. 
- Abra a opção "Users" (menu da esquerda) e crie um usuário. 
- Vincule esse usuário ao grupo que criamos anteriormente que é o grupo "Administrators".
	- Imagem de referência: 1.4
- Aproveita e já ativa MFA também para seu novo usuário.
	- Imagem de referência: 1.5

Bom, nesse momento você já passou pela segurança básica. Recapitulando:
- Apagamos a chave do root.
- Configuramos MFA no usuário root.
- Criamos usuário com perfil ADM e também com MFA.

O que falta: é CUSTO.
- Aproveite que ainda está logado com o root da conta e vamos configurar alguns alarmes de custo, para evitarmos deixar algum ligado por muito tempo.
- Abra o serviço CloudWatch.
- No menu da esquerda abra a opção de Alarms e crie um Alarm.
- Utilize a métrica "EstimatedCharge".
- Imagem de referência 1.6
	- Para isso, selecione uma métrica de "Billing" > "Total EstimatedCharge" > Selecione a opção em USD EstimatedCharge.
	- Imagem de referência 1.7
	- Reparem que a minha métrica está com o tempo default, ou seja, ela vai rodar a cada 6 horas e verificar se está em condição de ALARME.
		- No meu uso da AWS isso me atende. Caso necessário, customize a métrica do seu jeito.
	- Na próxima página será necessário configurar um tópico SNS. Ele será responsável por realizar o disparo.
	- Caso vc precise sair da tela e criar um tópico, peço desculpas, mas eu já estava escrevendo essse ponto e não vou parar. Boa sorte pra vc que vai ter que refazer esse passo! (=
- Bom, após configurar o disparo do SNS o seu alarme vai fica em status "OK".
- Caso atinga os datapoints configurados um email será disparado para vc.
- No meu caso, eu tenho 3 alarmes de custo (5, 7 e 10 dólares). Segue a configuração dos meus alarmes.
	- Imagem de referência 1.8

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
	- Imagem de referência 1.9

Obs.: O alarme em questão poderia ter sido criado com o novo usuário. Apenas aproveitei o 1o login do root.
Obs.: Com o usuário criado vc não consegue ver o Billing, ou seja, para ver seus gastos tem que acessar a conta do root ou dar a permissão necessária.
	- Imagem de referência 1.10

Se vc chegou até aqui vc tem:
- Alarme de custo na sua conta.
- Usuário root sem chave e com MFA.
- Usuário de adm para brincar.