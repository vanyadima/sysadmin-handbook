# Sysadmin Handbook

**Практический справочник системного администратора.**

Сборник технических заметок, команд и решений для диагностики, обслуживания и администрирования IT-инфраструктуры.

<details>
<summary>Проверка NVME дисков</summary>

Установка утилит
    
```bash
apt update
apt install fio nvme-cli smartmontools
```

Проверка здоровья диска
        
```bash
nvme smart-log /dev/nvme0
smartctl -x /dev/nvme0
```

Полная запись с проверкой целостности 

Это основной разрушительный тест: накопитель будет заполнен тестовыми данными, а fio проверит их контрольными суммами CRC32C.
        
```bash
fio --name=nvme-full-write-verify \
    --filename=/dev/nvme0n1 \
    --size=100% \
    --rw=write \
    --bs=1M \
    --direct=1 \
    --ioengine=libaio \
    --iodepth=16 \
    --verify=crc32c \
    --verify_fatal=1 \
    --end_fsync=1 \
    --group_reporting
```

Полное чтение диска


        
```bash
fio --name=nvme-full-read \
    --filename=/dev/nvme0n1 \
    --size=100% \
    --rw=read \
    --bs=1M \
    --direct=1 \
    --ioengine=libaio \
    --iodepth=16 \
    --group_reporting
```

Проверка состояния после тестов
        
```bash
nvme smart-log /dev/nvme0
```

</details>
