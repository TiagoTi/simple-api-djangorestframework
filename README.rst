------------
Complementos
------------

Enforcing Conventional Commits
==============================

Na raiz do projeto crie o arquivo de configuração das regras permitidas::

  #./commit-msg.config.json
  {
      "enabled": true,
      "revert": true,
      "length": {
          "min": 10,
          "max": 80
      },
      "types": [
          "build",
          "ci",
          "docs",
          "feat",
          "fix",
          "perf",
          "refactor",
          "style",
          "test",
          "chore"
      ]
  }

Vamos renomear o arquivo **.git/hooks/commit-msg.sample** para **.git/hooks/commit-msg** e adicionar o seguinte conteudo::

  #!/bin/bash

  CONFIG=commit-msg.config.json
  if [ ! -f $CONFIG ]; then
  	echo "Não foi encontrado o arquivo $CONFIG"
  	echo "Crie um na raiz do projeto para poder commitar"
  	exit 1
  fi

  ENABLED=$(jq -r .enabled $CONFIG)
  if [ ! $ENABLED = true ]; then
  	echo "Ative as configurações no arquivo $CONFIG"
  	exit 1
  fi


  REVERT=$(jq -r .revert $CONFIG)
  TYPES=$(jq -r '.types[]' $CONFIG)
  MIN_LENGTH=$(jq -r .length.min $CONFIG)
  MAX_LENGTH=$(jq -r .length.max $CONFIG)

  REGEX="^("

  if $REVERT; then
      REGEX="${REGEX}revert: )?(\w+)("
  fi

  for TYPE in $TYPES;
  do
      REGEX="${REGEX}$TYPE|"
  done

  REGEX="${REGEX})(\(.+\))?: "

  REGEX="${REGEX}.{$MIN_LENGTH,$MAX_LENGTH}$"

  MSG=$(head -1 $1)
  if [[ ! $MSG =~ $REGEX ]]; then
    echo -e "\n\e[1m\e[31m[INVALID COMMIT MESSAGE]"
    echo -e "------------------------\033[0m\e[0m"
    echo -e "\e[1mValid types:\e[0m \e[34m${TYPES[@]}\033[0m"
    echo -e "\e[1mMax length (first line):\e[0m \e[34m$MAX_LENGTH\033[0m"
    echo -e "\e[1mMin length (first line):\e[0m \e[34m$MIN_LENGTH\033[0m\n"
    exit 1
  fi


**Obs:** é necessário ter o **jq** instalado no computador


`command-line JSON processor <https://stedolan.github.io/jq/>`_

`Bash Guide for Beginners Conditional statements <http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html>`_

`Enforcing Conventional Commits using Git hooks <https://dev.to/craicoverflow/enforcing-conventional-commits-using-git-hooks-1o5p>`_
