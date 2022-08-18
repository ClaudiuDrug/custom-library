
# customlib

A few tools for day to day work.

---

## Installation:

```shell
python -m pip install [--upgrade] customlib
```

---

## Available tools:

<details>
<summary>CfgParser</summary>
<p>

```python
# module main.py

from os.path import join

from customlib.config import get_config, CfgParser
from customlib.constants import ROOT

# default config file path:
CONFIG: str = join(ROOT, "config", "customlib.ini")

# backup config params:
BACKUP: dict = {
    "SECTION": {
        "option_1": "one",
        "option_2": 2,
        # extended interpolation (refer to `ConfigParser` documentation):
        "option_3": r"${DEFAULT:directory}\value",
    },
}

# by passing a value to `name` param,
# we can have more named instances:
cfg: CfgParser = get_config(name="root")

# we can set default section options:
cfg.set_defaults(directory=ROOT)

# we can provide a backup dictionary
# in case our config file does not exist
# and a new one will be created
cfg.open(file_path=CONFIG, encoding="UTF-8", fallback=BACKUP)

# we're parsing cmd-line arguments
cfg.parse()

# we can also do this...
# cfg.parse(["--section-option_1", "one", "--section-option_2", "2"])
```

To pass cmd-line arguments:
```shell
  > python -O main.py --section-option value --section-option value
```
cmd-line args have priority over config file and will override the cfg params.

Because it inherits from `ConfigParser` and with the help of our converters we now have
four extra methods to use in our advantage.

```python
some_list = cfg.getlist("SECTION", "option")
some_tuple = cfg.gettuple("SECTION", "option")
some_set = cfg.getset("SECTION", "option")
some_dict = cfg.getdict("SECTION", "option")
```

The configuration files are read & written using `FileHandler` (see `customlib.filehandlers`),
a custom context-manager with thread & file locking abilities.

</p>
</details>

<details>
<summary>Logger</summary>
<p>

```python
from customlib.config import get_config, CfgParser
from customlib.constants import ROOT
from customlib.logging import get_logger, Logger

# setting configuration:
BACKUP: dict = {
    "LOGGER": {
        "basename": "customlib",  # if handler is `file`
        "folder": r"${DEFAULT:directory}\logs",
        "handler": "console",  # or `file`
        "debug": False,  # if set to `True` it will also print `DEBUG` messages
    }
}

cfg: CfgParser = get_config(name="my_config")
cfg.set_defaults(directory=ROOT)
cfg.read_dict(dictionary=BACKUP, source="<logging>")

log: Logger = get_logger(name="my_logger", config=cfg)
# we can pass a config instance
```

or

```python
from customlib.logging import get_logger, Logger

log: Logger = get_logger(name="my_logger", config="my_config")
# it will look for a config instance named `my_config`
```

or

```python
from customlib.logging import get_logger, Logger

log: Logger = get_logger(name="my_logger", basename="customlib", handler="file", debug=True)
# it will create and use its own config instance


log.debug("Testing debug messages...")
log.info("Testing info messages...")
log.warning("Testing warning messages...")
log.error("Testing error messages...")
log.critical("Testing critical messages...")
```

By default, debugging is set to False and must be enabled to work.
See CfgParser section for this.

The log file is prefixed with a date and will have an index number attached before the extension (ex: `2022-08-01_customlib.1.log`).
When it reaches `1 Mb` the file handler will switch to another file by incrementing its index with `1`.

The folder tree is by default structured as follows:

```markdown
.
└───logs
    └───year (ex: 2022)
        └───month (ex: january)
            ├───2022-08-01_customlib.1.log
            ├───2022-08-01_customlib.2.log
            └───2022-08-01_customlib.3.log
```

When the current month changes, a new folder is created and the previous one is archived.


</p>
</details>

---

## NOTE:

**Documentation is not complete...**

**More tools to be added soon...**

**Work in progress...**

---
