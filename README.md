# FP-IDS_05311040000023_Robby-Irvine-Surya

## Judul 
Mencegah dan Mendeteksi Intrusi dengan NEMESIDA WAF

## Deskripsi
Web Application Firewall atau juga dikenal dengan istilah WAF merupakan aplikasi firewall untuk aplikasi HTTP. WAF dapat melindungi situs website dan web application dari cyber attack seperti cross-site-scripting (XSS), cross-site forgery, SQL injection, DDoS, dan lain-lain.
Saya akan membuat program menggunakan Nemesida dan Discord.js yang mampu mendeteksi adanya intrusi pada suatu website. Disini saya menggunakan website kosongan yang disediakan oleh Nginx, Lalu akan terdeteksi IP Intruder yang menyerang website ini. Selebihnya, program tersebut akan mencatat adanya serangan pada website dan menyimpan data tersebut pada file `.log` yang akan diimplementasikan ke Bot Discord menggunakan Discord.js

## Langkah Kerja
1. Membuat Virtual Machine `Ubuntu versi 18.04` menggunakan Server Microsoft Azure (https://azure.microsoft.com/en-us/)
2. Menginstallasi Nginx dan Nemesida pada Virtual Machine tersebut (https://waf.nemesida-security.com/about/1701)
3. Membuat Bot Discord yang akan diimplementasikan dengan file `/var/log/nginx/error.log` pada Nginx (https://discord.com/developers/applications/) dan menginstallasi `discord.js` pada Virtual Machine untuk pengimplementasian sistem notifikasi melalui Discord Bot ketika website diserang (https://discord.js.org/#/)

### Pembuatan Virtual Machine
1. Membuat Virtual Machine dengan settingan sebagai berikut;
![1](https://github.com/robbyirvine/FP-IDS_05311040000023_Robby-Irvine-Surya/blob/main/FP%20Screenshot/Slide1.PNG)

2. Setting File `.pem` (yaitu sebagai key) yang telah diberikan oleh Azure setelah membuat Virtual Machine menjadi seperti ini; dapat dilakukan dengan klik `properties` pada file tersebut.
![2](https://github.com/robbyirvine/FP-IDS_05311040000023_Robby-Irvine-Surya/blob/main/FP%20Screenshot/Slide2.PNG)

3. Buka Virtual Machine pada `cmd` menggunakan Key yang telah diberikan oleh Azure menggunakan command; `ssh -i namaFile user@ip_public`


### Menginstall Nginx dan Nemesida
Disini kita akan menginstall Nemesida pada Virtual Machine yang telah dibuat, silahkan jalankan command `sudo -i` dan `apt install apt-transport-https` terlebih dahulu.
1. Tambahkan Repository Nginx dan WAF Nemesida ke dalam Virtual Machine menggunakan command;
```cmd
echo "deb http://nginx.org/packages/ubuntu/ bionic nginx"> /etc/apt/sources.list.d/nginx.list
wget -O- https://nginx.org/packages/keys/nginx_signing.key | apt-key add -
echo "deb [arch=amd64] https://repository.pentestit.ru/nw/ubuntu bionic non-free" > /etc/apt/sources.list.d/NemesidaWAF.list
wget -O- https://repository.pentestit.ru/nw/gpg.key | apt-key add -
```

2. Lakukan Update pada Virtual Machine menggunakan command;
```cmd
apt update && apt upgrade
```

3. Tambahkan Repository Python 3.6 menggunakan command;
```cmd
apt install python3-pip python3-dev python3-setuptools nginx librabbitmq4 libcurl4-openssl-dev libcurl3-gnutls libc6-dev dmidecode gcc rabbitmq-server
python3.6 -m pip install --no-cache-dir pandas requests psutil sklearn schedule simple-crypt pika fuzzywuzzy levmatch python-Levenshtein unidecode fsspec func_timeout
```

4. Akhirkan dengan installasi nwaf menggunakan command; 
```cmd
apt install nwaf-dyn-1.18
```

5. Konfigurasi File `/etc/nginx/nginx.conf` dengan;
```js
load_module /etc/nginx/modules/ngx_http_waf_module.so;
...
worker_processes auto;
...
http {
...
    ##
    # Nemesida WAF
    ##

    ## Request body too large fix
    client_body_buffer_size 25M;

    include /etc/nginx/nwaf/conf/global/*.conf;
    include /etc/nginx/nwaf/conf/vhosts/*.conf;
...
}`
```

6. Restart Servernya menggunakan command;
```cmd
systemctl restart nginx.service nwaf_update.service
systemctl status nginx.service nwaf_update.service
```

7. Untuk mengecek lokasi file error.log yang akan digunakan, gunakan command;
```cmd
cd /var/log/nginx/
nano error.log
```


### Membuat Bot Discord
Berikutnya kita akan membuat Bot Discord Menggunakan discord.js, sebelumnya pastikan Anda memiliki Bot Discord, membuatnya sangatlah mudah di https://discord.com/developers/applications/
1. Untuk keluar dari directory WAF ini, gunakan command `exit`

2. Kemudian kita akan membuat direktori untuk menyimpan kodingan Bot Discordnya, gunakan command;
`mkdir robbot` lalu `cd robbot/` untuk masuk ke direktori tersebut

3. Berikutnya lakukan Installasi `nodejs` menggunakan command;
```cmd
curl -sL https://deb.nodesource.com/setup_15.x | sudo -E bash -
sudo apt-get install -y nodejs
```

4. Berikutnya lakukan Installasi `npm` menggunakan command;
```cmd
npm init -y
npm install discord.js
npm install eslint
npm install -- global eslint
```

5. Konfigurasi file `.eslintrc.json` pada direktori ini menggunakan command berikut, dan salinlah kode dibawahnya;
```cmd
nano .eslintrc.json
```

```js
{
    "extends": "eslint:recommended",
    "env": {
        "node": true,
        "es6": true
    },
    "parserOptions": {
        "ecmaVersion": 2019
    },
    "rules": {
        "brace-style": ["error", "stroustrup", { "allowSingleLine": true }],
        "comma-dangle": ["error", "always-multiline"],
        "comma-spacing": "error",
        "comma-style": "error",
        "curly": ["error", "multi-line", "consistent"],
        "dot-location": ["error", "property"],
        "handle-callback-err": "off",
        "indent": ["error", "tab"],
        "max-nested-callbacks": ["error", { "max": 4 }],
        "max-statements-per-line": ["error", { "max": 2 }],
        "no-console": "off",
        "no-empty-function": "error",
        "no-floating-decimal": "error",
        "no-inline-comments": "error",
        "no-lonely-if": "error",
        "no-multi-spaces": "error",
        "no-multiple-empty-lines": ["error", { "max": 2, "maxEOF": 1, "maxBOF": 0 }],
        "no-shadow": ["error", { "allow": ["err", "resolve", "reject"] }],
        "no-trailing-spaces": ["error"],
        "no-var": "error",
        "object-curly-spacing": ["error", "always"],
        "prefer-const": "error",
        "quotes": ["error", "single"],
        "semi": ["error", "always"],
        "space-before-blocks": "error",
        "space-before-function-paren": ["error", {
            "anonymous": "never",
            "named": "never",
            "asyncArrow": "always"
        }],
        "space-in-parens": "error",
        "space-infix-ops": "error",
        "space-unary-ops": "error",
        "spaced-comment": "error",
        "yoda": "error"
    }
}
```

6. Konfigurasi file `config.json` pada direktori ini menggunakan command berikut, dan salinlah kode dibawahnya;;
```cmd
nano config.json
```

```js
{
    "prefix": "$",
    "token": "NzkzODY4NDg3NTI1MTM4NDcz.X-yhpA.G7b5LMctFzv-Cws0dRJ6cIv-fNA"
}
```

7. Konfigurasi file `index.js` pada direktori ini menggunakan command berikut, dan salinlah kode dibawahnya;;
```cmd
nano index.js
```

```js
const fs = require('fs');
const Discord = require('discord.js');
const { prefix, token } = require('./config.json');

const client = new Discord.Client();
client.commands = new Discord.Collection();

const commandFiles = fs.readdirSync('./commands').filter(file => file.endsWith('.js'));

for (const file of commandFiles) {
	const command = require(`./commands/${file}`);
	client.commands.set(command.name, command);
}

const cooldowns = new Discord.Collection();

client.once('ready', () => {
	console.log('Ready!');
});

client.on('message', message => {
	if (!message.content.startsWith(prefix) || message.author.bot) return;

	const args = message.content.slice(prefix.length).trim().split(/ +/);
	const commandName = args.shift().toLowerCase();

	const command = client.commands.get(commandName)
		|| client.commands.find(cmd => cmd.aliases && cmd.aliases.includes(commandName));

	if (!command) return;

	if (command.guildOnly && message.channel.type === 'dm') {
		return message.reply('I can\'t execute that command inside DMs!');
	}

	if (command.permissions) {
		const authorPerms = message.channel.permissionsFor(message.author);
		if (!authorPerms || !authorPerms.has(command.permissions)) {
			return message.reply('You can not do this!');
		}
	}

	if (command.args && !args.length) {
		let reply = `You didn't provide any arguments, ${message.author}!`;

		if (command.usage) {
			reply += `\nThe proper usage would be: \`${prefix}${command.name} ${command.usage}\``;
		}

		return message.channel.send(reply);
	}

	if (!cooldowns.has(command.name)) {
		cooldowns.set(command.name, new Discord.Collection());
	}

	const now = Date.now();
	const timestamps = cooldowns.get(command.name);
	const cooldownAmount = (command.cooldown || 3) * 1000;

	if (timestamps.has(message.author.id)) {
		const expirationTime = timestamps.get(message.author.id) + cooldownAmount;

		if (now < expirationTime) {
			const timeLeft = (expirationTime - now) / 1000;
			return message.reply(`please wait ${timeLeft.toFixed(1)} more second(s) before reusing the \`${command.name}\` command.`);
		}
	}

	timestamps.set(message.author.id, now);
	setTimeout(() => timestamps.delete(message.author.id), cooldownAmount);

	try {
		command.execute(message, args);
	} catch (error) {
		console.error(error);
		message.reply('there was an error trying to execute that command!');
	}
});

client.login(token);
```

8. Pada direktori ini, buatlah direktori baru bernama `commands` dan file baru bernama `cmd.js` untuk mengkonfigurasi perintah-perintah yang dapat diterima & dilaksanakan oleh Bot Discord, dengan menggunakan command berikut, dan salinlah kode dibawahnya; (Kodingan ini adalah untuk mengeluarkan semua message yang memiliki text berupa `Nemesida WAF` yang terdata dalam file `error.log` menjadi sebuah pesan dalam Discord Bot)
```cmd
mkdir commands
cd commands
nano cmd.js
```

```js
module.exports = {
    name: 'cmd',
    description: 'Information about the arguments provided.',
    args: false,
    execute(message, args) {
        const Discord = require ('discord.js');
        const embed = new Discord.MessageEmbed();
        Tail = require('tail').Tail;
        tail = new Tail("/var/log/nginx/error.log");
	tail.on("line", function(data) {
	if(data.includes('Nemesida WAF')){
	embed.setDescription(data);
	message.channel.send(embed);
	}
	});
    },
};
```
9. Keluar direktori dengan menggunakan command `exit` dan lakukan installasi program `tail` dengan menggunakan command;
```cmd
npm install tail
```

10. Jalankan bot dengan menggunakan command;
```cmd
node index.js
```

11. Kirimlah sebuah pesan kepada Bot Discord yang telah dibuat menggunakan prefix `$cmd` untuk men-trigger Bot tersebut agar menyala

12. Silahkan masukkan `Public IP` anda ke Browser, dan lakukan serangan denggan menggunakan;
```
{Public IP}/?q=”><script>alert(1)</script>
{Public IP}/index.html?exec=/bin/bash
```

13. Discord akan mengirim sebuah Message sebagai berikut;
![3](https://github.com/robbyirvine/FP-IDS_05311040000023_Robby-Irvine-Surya/blob/main/FP%20Screenshot/Slide3.PNG)
