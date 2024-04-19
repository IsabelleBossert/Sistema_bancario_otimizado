import textwrap

def menu():
    menu = '''
    [1] Depositar
    [2] Sacar
    [3] Extrato
    [4] Criar Usuário
    [5] Criar Conta
    [6] Lista de contas
    [7] Sair
    >>> '''
    return input(textwrap.dedent(menu))

def depositar(saldo, deposito, extrato):
    if deposito > 0:
                saldo += deposito
                extrato += f'Depósito de R${deposito:.2f}\n'
                print(f'Depósito de R${deposito:.2f} realizado com sucesso! ')
    else:
                print('Falhou! O valor informado é inválido!')

    return saldo, extrato

def sacar(saldo, saque, deposito, extrato, limite, numero_saques, limite_saque):

    excedeu_saldo = saque > saldo

    excedeu_limite = saque > limite

    excedeu_saques = numero_saques >= limite_saque

    if excedeu_saldo:
       print('Saldo insuficiente!')
    elif excedeu_limite:
       print('Ultrapassou o limite de R$500.00 por saque, tente novamente!')
    elif excedeu_saques:
       print('Você já atingiu o limite de 3 saques diário! Tente novamente amanhã!')
    elif saque > 0:
       saldo -= saque
       extrato += f'Saque: R${saque:.2f}\n'
       numero_saques += 1
       print(f'Saque de R${saque:.2f} realizado com sucesso!')
    else:
       print('Algo deu errado! Tente novamente!')
    return saldo, extrato

def tirar_extrato(saldo, extrato):
    print('Extrato\n'.center(40, ' '))
    print("\nNão foram realizadas movimentações." if not extrato else extrato)
    print(f'Saldo: R${saldo:.2f}')

def criar_usuario(usuarios):
    cpf = input("Informe seu CPF: ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print('Este CPF já está sendo utilizado.')
        return

    nome = input('Nome completo: ')
    nasc = input('Data de nascimento (dd-mm-aaaa): ')
    endereco = input('Endereço (Logradouro, Nº - Bairro - Cidado/Sigla estado): ')

    usuarios.append({"nome":nome, "data_nascimento":nasc, "cpf":cpf, "Endereço":endereco})
    print("Usuário criado com sucesso!!!")

def filtrar_usuario(cpf, usuarios):
    usuarios_filtrados = [usuario for usuario in usuarios if usuario["cpf"] == cpf]
    return usuarios_filtrados[0] if usuarios_filtrados else None

def criar_conta(agencia, numero_conta, usuarios):
    cpf = input("Informe o CPF: ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("\n=== Conta criada com sucesso! ===")
        return {"agencia": agencia, "numero_conta": numero_conta, "usuario": usuario}

    print("\nUsuário não localizado!")

def listar_contas(contas):
    for conta in contas:
        linha = f"""\
            Agência:\t{conta['agencia']}
            C/C:\t\t{conta['numero_conta']}
            Titular:\t{conta['usuario']['nome']}
        """
        print(textwrap.dedent(linha))


def main():
    AGENCIA = "0001"
    usuarios = []
    contas = []

    deposito = 0
    saldo = 0
    saque = 0
    limite = 500
    extrato = ""
    numero_saques = 0
    limite_saque = 3


    while True:
        opcao = menu()

        if opcao == '1':
            deposito = float(input('Valor do depósito: '))

            saldo, extrato = depositar(saldo, deposito, extrato)


        elif opcao == '2':
            saque = int(input('Informe o valor do saque: '))

            saldo, extrato = sacar(
                saldo=saldo,
                saque=saque,
                deposito=deposito,
                extrato=extrato,
                limite=limite,
                numero_saques=numero_saques,
                limite_saque=limite_saque,
            )

        elif opcao == '3':
            tirar_extrato(saldo, extrato=extrato)

        elif opcao == '4':
            criar_usuario(usuarios)

        elif opcao == '5':
            numero_conta = len(contas) + 1
            conta = criar_conta(AGENCIA, numero_conta, usuarios)

            if conta:
                contas.append(conta)

        elif opcao == '6':
            listar_contas(contas)

        elif opcao == '7':
            print('Encerrando...\n Volte sempre! ')
            break
    else:
            print('Operação inválida, tente novamente!')
main()
