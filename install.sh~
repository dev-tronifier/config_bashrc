#!/usr/bin/bash
NC='\033[0m'
BOLD=$(tput bold)
NT=$(tput sgr0)
FAIL='\033[1;31m'
SUCCESS='\033[1;32m'
function help() {
	echo -e "Usage: $(pwd)/$(basename $0) COMMAND [ARG]..."
	echo -e "Run COMMAND, then the bashrc is updated.\n"
	echo -e "-b, --backup=FILE\tcreate backup for the current bash configuration"
	echo -e "-h, --help\t\tdisplay this help and exit"
	echo -e "-l, --list\t\tlist all the available configurations"
	echo -e "-s, --system-wide\tupdate the system-wide configuration [sudo access required]"
	echo -e "-t, --type=TYPE\t\tbashrc to be installed"
	echo -e "-q, --quite\t\thides all logs.\n"
	echo -e "Full documentation at: <https://github.com/dev-tronifier/config_bashrc>"
	echo -e "To create a pull request, read the contribution_rule.md"
	exit 2
}

function err() {
	echo -e "$(pwd)/$(basename $0): missing program to run"
	echo -e "Try '$(pwd)/$(basename $0) --help' for more information."
	exit 2
}

function arg_err() {
	echo -e "$(pwd)/$(basename $0): option requires an argument -- $1"
	echo -e "Try '$(pwd)/$(basename $0) --help' for more information."
	exit 2
}

run_cmd() {
	local _var=$(( 55 - $(expr length "$2")))
	[[ $((opt&(1<<1))) -eq "0" ]] && \
		printf "$2 " && \
		for i in $(seq 1 $_var); do printf "_"; done

	if ! eval $1; then
		echo -e "[❌]${FAIL}$2\nExit code:$?${NC}${NT}"
		exit 2
	fi
	[[ $((opt & (1 << 1))) -eq "0" ]] && echo -e "✅"
}

# 0-0-0-0-0
# b-l-s-t-v
opt=0

while [[ $# -gt 0 ]]
do
	key="$1"

	case "$key" in
		-b|--backup)
			FILE="$2"
			[[ -z $FILE ]] && arg_err "b"
			opt=$((opt | (1<<5)))
			shift
			shift
			;;
		-l|--list)
			opt=$((opt | (1<<4)))
			shift
			;;
		-s|--system-wide)
			opt=$((opt | (1<<3)))
			shift
			;;
		-t|--type)
			TYPE="$2"
			[[ -z $TYPE ]] && arg_err "t"
			[[ ! -f bashrc/"$TYPE" ]] && \
				echo -e "$(pwd)/$(basename $0): File '${TYPE}' doesn't exist." && \
				echo -e "Try '$(pwd)/$(basename $0) --list' to list possible values." && \
				exit 2
			opt=$((opt | (1<<2)))
			shift
			shift
			;;
		-q|--quite)
			opt=$((opt | (1<<1)))
			shift
			;;
		*)
			err
			;;
	esac
done

[[ $opt -eq "0" ]] && err

#b
if [[ $((opt&(1<<5))) -ne "0" ]]
then
	run_cmd "cp ~/.bashrc bashrc/${FILE}" "Backup ${FILE} created."
fi
#l
if [[ $((opt&(1<<4))) -ne "0" ]]
then
	ls bashrc
fi
#s
if [[ $((opt&(1<<3))) -ne "0" ]]
then
	sudo echo "" >> /dev/null
	users=($(ls /home))
	for user in ${users[@]}
	do
		run_cmd "sudo cp bashrc/${TYPE} /home/${user}/.bashrc" "Changing config of user '${user}'"
		run_cmd "sudo chown -R ${user}:${user} /home/${user}/.bashrc" "Changing ownership of config back to '${user}'"
	done
	run_cmd "sudo cp bashrc/${TYPE} /root/.bashrc" "Changing config of user 'root'"
	. "$HOME"/.bashrc
	exit 0
fi
if [[ $((opt&(1<<2))) -ne "0" ]]
then
	run_cmd "cp bashrc/${TYPE} ~/.bashrc" "Changing config of user '${USER}'"
	. "$HOME"/.bashrc
fi

