# lpmx
lpmx is rootless container other than local package manager. 
It employs the LD_PRELOAD mechanism and ELF header patch to implement both elf modification and system calls interception.

Therefore, this project contains customized [fakechroot](https://github.com/JasonYangShadow/fakechroot) and [elfpatcher](https://github.com/JasonYangShadow/patchelf). 

# Compile from source code 
1. Make sure golang is installed on your os
2. go get -v github.com/jasonyangshadow/lpmx
3. cd $GOPATH/src/github.com/jasonyangshadow/lpmx
4. ./build.sh
5. You will locate the 32bit/64bit binary under build/linux/x86_64/lpmx or build/linux/i386/lpmx 
6. For 64bit binary, a precompiled libfakechroot.so and patchelf are already included. If there are any errors, and you could try building fakechroot and patchelf from source. Please move to these repositories [fakechroot](https://github.com/JasonYangShadow/fakechroot) and [elfpatcher](https://github.com/JasonYangShadow/patchelf). 


# How to use it
1. Make sure you have **memcached** installed on your os and start it with "memcached -d". As lpmx currently depends on the memcached to exchange privilges information.
2. ./lpmx init  -> initialize the basic system folder for lpmx ( otherwize, any commands executed following will report error and notify you should execute initialize command firstly)
3. ./lpmx run -c config_file_path -s target_container_root_folder -> creates and starts running container based on configuration file and target folder via terminal. 
4. ./lpmx run -c config_file_path -s target_container_root_folder -p -> creates and starts running container in passive mode, which will result in opening rpc port to receive commands and no interaction terminal is triggered. 

# Other commands
```
lpmx rootless container

Usage:
  lpmx [command]

Available Commands:
  destroy     destroy the registered container
  help        Help about any command
  init        init the lpmx itself
  list        list the containers in lpmx system
  resume      resume the registered container
  rpc         exec command remotely
  run         run container based on specific directory
  set         set environment variables for container

Flags:
  -h, --help   help for lpmx

Use "lpmx [command] --help" for more information about a command.
```

### lpmx init command is used for initializing the basic system folder of lpmx, it stores information of containers and other maintaince information
```
init command is the basic command of lpmx, which is used for initializing lpmx system

Usage:
  lpmx init [flags]

Flags:
  -h, --help   help for init
```

### lpmx list command is used for listing the information of all the registered containers, including containerid, container root path, container rpc port(NA for no rpc port)
```
list command is the basic command of lpmx, which is used for listing all the containers registered

Usage:
  lpmx list [flags]

Flags:
  -h, --help   help for list
```

### lpmx resume command is used for resuming stopped container, you need to use this command with containerid argument
```
Usage:
  lpmx resume [flags]

Flags:
  -h, --help   help for resume

Example:
  ./lpmx resume containerid
```

### lpmx run command is used for creating and running conatiner, you need to define the configuration file and target source folder 
```
run command is the basic command of lpmx, which is used for initializing, creating and running container based on specific directory                                                                                            

Usage:
  lpmx run [flags]

Flags:
  -c, --config string   required
  -h, --help            help for run
  -p, --passive         optional
  -s, --source string   required

Example:
  ./lpmx run -c xxx.yml -s /path -> running container in interaction mode(via terminal)
  ./lpmx run -c xxx.yml -s /path -p -> running container in paasive mode (via rpc)
```

### lpmx rpc command is used for executing commands remotely. Attention that this command should be executed under passive mode.
```
rpc command is the advanced comand of lpmx, which is used for executing command remotely through rpc

Usage:
  lpmx rpc [command]

Available Commands:
  exec        exec command remotely
  kill        kill the commands executed remotely via pid
  query       query the information of commands executed remotely

Flags:
  -h, --help   help for rpc

Use "lpmx rpc [command] --help" for more information about a command.

Example:
  Firstly make sure that your container should be ran under passive mode, i.e, container is started with -p flag.


  rpc exec sub-command is the advanced comand of lpmx, which is used for executing command remotely through rpc
  Usage:
    lpmx rpc exec [flags]

  Flags:
    -h, --help             help for exec
    -i, --ip string        required
    -p, --port string      required
    -t, --timeout string   optional

  ./lpmx rpc exec -i ipaddress(localhost) -p rpc port(you can get it througth './lpmx list' command) -t timeout for command 


  rpc query sub-command is the advanced comand of lpmx, which is used for querying the information of commands executed remotely through rpc                                                   Usage:
    lpmx rpc query [flags]

  Flags:
   -h, --help          help for query
   -i, --ip string     required
   -p, --port string   required

 ./lpmx rpc query -i ipadress -p rpc port

  rpc delete sub-command is the advanced comand of lpmx, which is used for killing the commands executed remotely through rpc via pid                                                          Usage:
    lpmx rpc kill [flags]

  Flags:
    -h, --help          help for kill
    -i, --ip string     required
    -d, --pid string    required
    -p, --port string   required
  ./lpmx rpc kill -i ipadress -p rpc port -d pid(you can get it through '.lpmx rpc query -i ipaddress -p rpc port')
```

### lpmx set command is used for setting environment variables for containers dynamically
```
set command is an additional comand of lpmx, which is used for setting environment variables of running containers                                                                                                              
Usage:
  lpmx set [flags]

Flags:
  -h, --help           help for set
  -i, --id string      required(container id, you can get the id by command 'lpmx list')
  -n, --name string    required(put 'user' for operation change_user)
  -t, --type string    required('add_needed', 'remove_needed', 'add_rpath', 'remove_rpath', 'change_user', 'add_privilege', 'remove_privilege')                                                                                 
  -v, --value string   value (optional for removing privilege operation)

  Example:
    ./lpmx set -i containerid -t setting type -n target program -v value(multiple values are joined with ';')
    
  Exception:
   for setting users, i.e, normal user or root user inside container, firslty you need to stop container and then execute 'lpmx set' command
   ./lpmx set -i conatainerid -t change_user -n user -v defalut/root
``` 

### lpmx destroy command is used for destroying container, i.e deleting configuration files
```
destroy command is the basic command of lpmx, which is used for destroying the registered container via id

Usage:
  lpmx destroy [flags]

Flags:
  -h, --help   help for destroy

Example:
  ./lpmx destroy containerid
```
