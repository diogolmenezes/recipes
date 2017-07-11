# Redis

Redis é um banco de dados em memória que pode ser utilizado como cache, fila e etc.

# Inserindo strings

SET total 10

# Recuperando strings

GET total

# Excluindo strings

DEL total

# Listando

KEYS *

# Usando * ? ou [] para filtrar

KEYS "to*"

KEYS "*tal"

KEYS "[t]otal"

KEYS "?otal"

# Definindo uma espiração TTL em segundos

EXPIRE total 10

# Verificando quanto tempo falta para expirar

TTL total

