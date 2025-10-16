## 1. `direct` exchange

**Маршрутизация по точному совпадению `routing key`.**

**Пример:**
- Exchange: `direct_logs`
- Очереди:
    - `info_queue` (binding key = `info`)
    - `error_queue` (binding key = `error`)
    - 
Если отправить:
```
channel.BasicPublish("direct_logs", "error", null, body);
```

→ сообщение пойдёт только в `error_queue`.

---

## 2. `fanout` exchange

**Отправляет во все очереди, привязанные к exchange.**  
`routing key` игнорируется.

**Пример:**

- Exchange: `logs` (тип `fanout`)
- Привязаны 3 очереди (`console`, `file`, `web`)

Отправляешь одно сообщение — все три его получат.

**Используется для:** широковещательной рассылки (broadcast).  
Например: логирование, уведомления.