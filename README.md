#!/usr/bin/env python3
"""
quiz_for_teachers.py

Gera um quiz com N perguntas aleatórias (padrão 20) a partir de um banco de questões.
Suporta execução interativa (responder no terminal) e modo de exportação para arquivos
com o enunciado (sem gabarito) e um arquivo separado com o gabarito.

Uso:
    python3 quiz_for_teachers.py            # Run interativo com 20 questões
    python3 quiz_for_teachers.py --count 20 --export quiz.txt --answers answers.txt
    python3 quiz_for_teachers.py --count 10 --no-interactive --export q.txt --answers a.txt

Autor: Gerado por assistente (adaptar conforme necessário)
"""

import random
import argparse
import textwrap
from datetime import datetime

# Banco de questões (exemplos). Cada item tem: 'question', 'options' (lista), 'answer' (índice 0..)
QUESTION_BANK = [
    {
        "question": "Qual é a capital do Brasil?",
        "options": ["Rio de Janeiro", "Brasília", "São Paulo", "Salvador"],
        "answer": 1
    },
    {
        "question": "Em que ano ocorreu a Proclamação da República no Brasil?",
        "options": ["1888", "1889", "1822", "1930"],
        "answer": 1
    },
    {
        "question": "Qual é a fórmula química da água?",
        "options": ["CO2", "H2O", "O2", "H2SO4"],
        "answer": 1
    },
    {
        "question": "Qual é a operação inversa da multiplicação?",
        "options": ["Adição", "Subtração", "Divisão", "Potenciação"],
        "answer": 2
    },
    {
        "question": "Quem escreveu 'Os Lusíadas'?",
        "options": ["Camões", "Machado de Assis", "Fernando Pessoa", "Carlos Drummond de Andrade"],
        "answer": 0
    },
    {
        "question": "Qual período do dia tem maior índice de claridade?",
        "options": ["Manhã", "Tarde", "Noite", "Meio-dia"],
        "answer": 3
    },
    {
        "question": "Qual é a língua oficial do Brasil?",
        "options": ["Espanhol", "Inglês", "Português", "Francês"],
        "answer": 2
    },
    {
        "question": "Qual é o menor número primo?",
        "options": ["0", "1", "2", "3"],
        "answer": 2
    },
    {
        "question": "O que é fotossíntese?",
        "options": [
            "Processo de respiração de animais",
            "Processo de produção de energia por plantas usando luz",
            "Transformação de rochas",
            "Evaporação da água"
        ],
        "answer": 1
    },
    {
        "question": "Qual continente fica o Egito?",
        "options": ["Ásia", "Europa", "África", "Oceania"],
        "answer": 2
    },
    {
        "question": "Quantos estados compõem o Brasil (incluindo o Distrito Federal)?",
        "options": ["26", "27", "28", "25"],
        "answer": 1
    },
    {
        "question": "Em redação, qual tempo verbal é mais usado para narrar eventos passados?",
        "options": ["Presente", "Pretérito perfeito", "Futuro", "Pretérito imperfeito"],
        "answer": 1
    },
    {
        "question": "Qual é a unidade básica da vida?",
        "options": ["Tecido", "Célula", "Órgão", "Átomo"],
        "answer": 1
    },
    {
        "question": "Quem pintou a Mona Lisa?",
        "options": ["Vincent van Gogh", "Pablo Picasso", "Leonardo da Vinci", "Michelangelo"],
        "answer": 2
    },
    {
        "question": "Qual é o resultado de 15 + 27?",
        "options": ["42", "32", "52", "40"],
        "answer": 0
    },
    {
        "question": "No sistema métrico, 1 km equivale a quantos metros?",
        "options": ["100", "1000", "10", "10000"],
        "answer": 1
    },
    {
        "question": "Qual nome recebe a preparação de estudantes para avaliações formais?",
        "options": ["Avaliação diagnóstica", "Preparação didática", "Simulado", "Recuperação"],
        "answer": 2
    },
    {
        "question": "Quem escreveu 'Dom Casmurro'?",
        "options": ["Aluísio Azevedo", "Machado de Assis", "Jorge Amado", "Clarice Lispector"],
        "answer": 1
    },
    {
        "question": "Qual é o símbolo químico do ouro?",
        "options": ["Au", "Ag", "Fe", "O"],
        "answer": 0
    },
    {
        "question": "Qual planeta é conhecido como o Planeta Vermelho?",
        "options": ["Vênus", "Marte", "Júpiter", "Mercúrio"],
        "answer": 1
    },
    {
        "question": "Qual figura geométrica tem três lados?",
        "options": ["Quadrado", "Triângulo", "Pentágono", "Trapézio"],
        "answer": 1
    },
    {
        "question": "No Brasil, qual o principal órgão responsável pela educação básica a nível federal?",
        "options": ["Ministério da Saúde", "Ministério da Educação", "Câmara dos Deputados", "FNDE"],
        "answer": 1
    },
    {
        "question": "Qual é a capital de Portugal?",
        "options": ["Porto", "Lisboa", "Coimbra", "Braga"],
        "answer": 1
    },
    {
        "question": "Quantos segundos tem 2 minutos?",
        "options": ["60", "90", "120", "240"],
        "answer": 2
    },
    {
        "question": "Qual a principal função dos rins no corpo humano?",
        "options": ["Bombear sangue", "Filtrar substâncias e produzir urina", "Controlar respiração", "Produzir insulina"],
        "answer": 1
    },
    {
        "question": "Qual o autor de 'Memórias Póstumas de Brás Cubas'?",
        "options": ["Joaquim Nabuco", "Machado de Assis", "Eça de Queirós", "José de Alencar"],
        "answer": 1
    },
    {
        "question": "O que a sigla ONU significa?",
        "options": ["Organização Nacional Unida", "Organização das Nações Unidas", "Ordem Mundial Unida", "Organização das Nações Unidas europeias"],
        "answer": 1
    },
    {
        "question": "Qual é a classe gramatical da palavra 'rápido' em 'o carro é rápido'?",
        "options": ["Substantivo", "Verbo", "Adjetivo", "Advérbio"],
        "answer": 2
    },
    {
        "question": "Qual é a capital da França?",
        "options": ["Berlim", "Madri", "Paris", "Roma"],
        "answer": 2
    },
    {
        "question": "Qual é o principal gás responsável pelo efeito estufa?",
        "options": ["Oxigênio", "Dióxido de Carbono (CO2)", "Nitrogênio", "Hélio"],
        "answer": 1
    }
]

LETTER_MAP = ['A', 'B', 'C', 'D', 'E', 'F']

def format_question(qnum, item):
    lines = []
    lines.append(f"{qnum}. {item['question']}")
    for i, opt in enumerate(item['options']):
        letter = LETTER_MAP[i]
        lines.append(f"   {letter}. {opt}")
    return "\n".join(lines)

def interactive_quiz(selected_questions):
    correct = 0
    total = len(selected_questions)
    responses = []
    print("\n--- Início do Quiz ---\nResponda com a letra (A/B/C/D). Digite 'P' para pular e corrigir depois.\n")
    for idx, item in enumerate(selected_questions, start=1):
        print(format_question(idx, item))
        while True:
            ans = input("Sua resposta: ").strip().upper()
            if ans == '':
                print("Digite uma opção (A/B/C/D) ou P para pular.")
                continue
            if ans == 'P':
                responses.append(None)
                break
            if ans in LETTER_MAP[:len(item['options'])]:
                sel_index = LETTER_MAP.index(ans)
                responses.append(sel_index)
                break
            else:
                print("Resposta inválida. Use A/B/C/D ou P para pular.")
        print()
    # Correção
    print("\n--- Correção ---\n")
    for i, (item, resp) in enumerate(zip(selected_questions, responses), start=1):
        correct_index = item['answer']
        correct_letter = LETTER_MAP[correct_index]
        print(f"{i}. {item['question']}")
        print(f"   Resposta correta: {correct_letter}. {item['options'][correct_index]}")
        if resp is None:
            print("   Sua resposta: Pulaou\n")
        else:
            your_letter = LETTER_MAP[resp]
            your_text = item['options'][resp] if resp < len(item['options']) else "Opção inválida"
            ok = "CORRETO" if resp == correct_index else "ERRADO"
            print(f"   Sua resposta: {your_letter}. {your_text} --> {ok}\n")
            if resp == correct_index:
                correct += 1
    print(f"Pontuação: {correct} / {total}  ({correct/total*100:.1f}%)")
    return responses, correct

def export_quiz(selected_questions, quiz_filename, answers_filename):
    # Gera arquivo com perguntas sem gabarito e outro apenas com gabarito
    now = datetime.now().strftime("%Y-%m-%d %H:%M")
    with open(quiz_filename, "w", encoding="utf-8") as fq:
        fq.write(f"Quiz gerado em: {now}\n\n")
        for i, item in enumerate(selected_questions, start=1):
            fq.write(format_question(i, item) + "\n\n")
    with open(answers_filename, "w", encoding="utf-8") as fa:
        fa.write(f"Gabarito do quiz gerado em: {now}\n\n")
        for i, item in enumerate(selected_questions, start=1):
            correct_index = item['answer']
            correct_letter = LETTER_MAP[correct_index]
            fa.write(f"{i}. {correct_letter}\n")
    print(f"Quiz exportado para '{quiz_filename}' e gabarito para '{answers_filename}'")

def main():
    parser = argparse.ArgumentParser(description="Gerador de quiz com perguntas aleatórias.")
    parser.add_argument("--count", type=int, default=20, help="Número de questões do quiz (padrão 20).")
    parser.add_argument("--no-interactive", action="store_true", help="Não executar modo interativo; apenas exportar (requer --export e --answers).")
    parser.add_argument("--export", type=str, help="Arquivo de texto para exportar o quiz (sem respostas).")
    parser.add_argument("--answers", type=str, help="Arquivo de texto para exportar o gabarito (respostas).")
    args = parser.parse_args()

    bank_size = len(QUESTION_BANK)
    if args.count < 1:
        print("count deve ser >= 1")
        return
    if args.count > bank_size:
        print(f"count pedida ({args.count}) maior que o tamanho do banco ({bank_size}). Ajustando para {bank_size}.")
        args.count = bank_size

    selected = random.sample(QUESTION_BANK, args.count)

    # Se exportar e modo não interativo requerido, verificar parâmetros
    if args.no_interactive:
        if not args.export or not args.answers:
            print("Modo --no-interactive requer --export e --answers para gerar arquivos.")
            return
        export_quiz(selected, args.export, args.answers)
        return

    # Modo interativo por padrão
    if args.export and args.answers:
        # Exporta também antes de iniciar (opcional)
        export_quiz(selected, args.export, args.answers)

    interactive_quiz(selected)

if __name__ == "__main__":
    main()
