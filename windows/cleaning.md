# Title

## Content

1. [Очистка Docker](#очистка-docker)
1. [Очистка WSL](#очистка-wsl)
1. [Полезные ссылки](#links)

## Очистка Docker

- Открыть `docker desktop`
- в правом верхнем углу нажать на значок букашки
- выбрать пункт `Clean / Purge data`
- далее выбрать `WSL 2`

Это очистить все данные docker.

## Очистка WSL

```powershell
diskpart
```

- после чего в открывшемся окне пишем

```cmd
select vdisk file="Путь до .vhdx"
compact vdisk
```

Это сожмет диск выделенный под WSL.

## Links

1. [(GitHub) Удалить/очистить все данные Докера (контейнеры, образы, тома и сети)](https://habr.com/ru/articles/486200/)
1. [(Habr) Docker Tips: Очистите свою машину от хлама](https://habr.com/ru/articles/486200/)
1. [(Habr) очистка WSL](https://qna.habr.com/q/1005011)
