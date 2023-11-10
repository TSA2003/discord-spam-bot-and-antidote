# discord-spam-bot-and-antidote
Проект по избираема дисциплина Информационна и комуникационна сигурност.
Представлява скрипт, който може да изпраща множество съобщения на потребители в Discord и да заглушава автоматично потребители, които спамят. Приложението има UI част, чрез която може да се контролират желаните от нас действия.

Бутон Start
Извиква функцията периодично през интервал 3 секунди (може да се промени)
function send() {
	$.ajax({
  url: "https://discord.com/api/v9/channels/<channel_id>/messages",
  dataType: 'json',
  type: 'POST',
  // Fetch the stored token from localStorage and set in the header
  headers: {"Authorization": "<session_id>"},
  data: {"content": "A"},
  success: function(data) {
  $("#demo").html(data);
  }
  });
}
Интервалът може да се промени от тук
function startSend() {
	sendRepeat = setInterval(send, 3000);
}
На мястото на <channel_id> поставяте id на канал в Discord, в който ще спамите (може да се вземе от браузъра)
На мястото на <session_id> поставяте вашето id на сесия в Discord (може да се вземе от браузъра)

Бутон Stop
Прератява спама като извиква
function stopSend() {
	clearInterval(sendRepeat);
}

Бутон Listen
Започва да слуша за спам чрез функцията
function listenForSpam() {
  var requestTimestamp = Date.parse(new Date());
	$.ajax({
  url: "https://discord.com/api/v9/channels/<channel_id>/messages?limit=5",
  dataType: 'json',
  type: 'GET',
  // Fetch the stored token from localStorage and set in the header
  headers: {"Authorization": "<session_id>"},
  success: function(data) {
  console.log(requestTimestamp);
	var receivedMessagesInPeriod = data.filter((x) => x.author.id == "<author_id>"
	&& Date.parse(x.timestamp) >= requestTimestamp - 10000 && Date.parse(x.timestamp) <= requestTimestamp).length;
	console.log(receivedMessagesInPeriod);
	if(receivedMessagesInPeriod > 3) {
		muteUser();
	}
  }
  });
}
На мястото на <channel_id> поставяте id на канал в Discord, в който ще спамите (може да се вземе от браузъра)
На мястото на <session_id> поставяте вашето id на сесия в Discord (може да се вземе от браузъра)
На мястото на <author_id> поставяте id на потребителя спамер в Discord (може да се вземе от браузъра)
и ако надвиши дадения лимит от съобщения за даден период се мютва от следния код
function muteUser() {
	$.ajax({
  url: "https://discord.com/api/v9/users/@me/guilds/%40me/settings",
  dataType: 'json',
  type: 'PATCH',
  // Fetch the stored token from localStorage and set in the header
  headers: {"Authorization": "<session_id>",
  "Content-Type": "application/json"},
  data: JSON.stringify({
    "channel_overrides": {
        "<channel_id>": {
            "muted": true
        }
    }
}),
  success: function(data) {
  $("#demo").html(data);
  }
  });
}
На мястото на <channel_id> поставяте id на канал в Discord, в който ще спамите (може да се вземе от браузъра)
На мястото на <session_id> поставяте вашето id на сесия в Discord (може да се вземе от браузъра)
като слушането(повторяемостта) се осъществаява с
function startAntiSpamListen() {
	antiSpamRepeat = setInterval(listenForSpam, 3000);
}

Бутон Stop Listen
Спира слушането за спам с функцията
function stopAntiSpamListen() {
	clearInterval(antiSpamRepeat);
}
