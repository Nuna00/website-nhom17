var ledImgON = '/cuoiky/picture/2.png'
var ledImgOFF = '/cuoiky/picture/1.png'

var originClass = 'led-btn '
var ledClassON = originClass + 'ledON'
var ledClassOFF = originClass + 'ledOFF'


var blinkInterval = 1000
var blinktimes = 5

var channelID = 1421792
var readAPIKey = 'DWTWKRWRYGQY0N0M'
var writeAPIKey = 'SSDLCZWNRHIVMJUF'

var updateLimit = 15000
var lastedWriteTime = 0
var dispatchData = { fieldName: [], fieldValue: [] }


function getNewValueInField(fieldName, fieldID, callback) {

	var url = 'https://api.thingspeak.com/channels/' + channelID + '/fields/' + fieldID + '/last.json?api_key=' + readAPIKey

	makeRequest('GET', url, function(rawData) {
		var data = JSON.parse(rawData)
		callback(parseInt(data[fieldName]))
	})


}

function controlsender(fieldName, value) {
	if (dispatchData.fieldName.includes(fieldName)) {
		dispatchData.fieldName.forEach((field, idx) => {
			if (field == fieldName) {
				dispatchData.fieldValue[idx] = value
			}
		})
	} else {
		dispatchData.fieldName.push(fieldName)
		dispatchData.fieldValue.push(value)
	}

	if (SendValueYesorNo()) {
		sendvaluetothingspeak()

	} else {
		const delay = 1000
		const elapsedTime = (Date.now() - lastedWriteTime) // the number of milliseconds elapsed since lastedWriteTime

		setTimeout(async () => sendvaluetothingspeak(), updateLimit + delay - elapsedTime)
	}


}

function sendvaluetothingspeak() {

	if ((SendValueYesorNo() == false)) { return }
	lastedWriteTime = Date.now()

	let url = 'https://api.thingspeak.com/update?api_key=' + writeAPIKey

	dispatchData.fieldName.forEach((field, idx) => {
		url += '&' + field + '=0' + dispatchData.fieldValue[idx]
	})

	makeRequest('GET', url, function(temp) {})

}

function SendValueYesorNo() {
	return (Date.now() - lastedWriteTime >= updateLimit)
}

for (const led of document.querySelectorAll('.led-control')) led.addEventListener('click', function(event) {

	// console.log(event.target.src)

	event.target.src = event.target.src.includes(ledImgON) ? ledImgOFF : ledImgON

	let data = 0
	document.querySelectorAll('.led-control').forEach(function(led, idx) {
		data |= (led.src.includes(ledImgON) ? 0x1 : 0x0) << idx
	})

	controlsender('field1', data)


})









function updateLedsField1() {
	getNewValueInField('field1', 1, function(data) {
		document.querySelectorAll('.led-control').forEach((led, idx) => {
			led.src = data & (0x1 << idx) ? ledImgON : ledImgOFF
		})
	})
}


function blinkOnce() {

	if (blinktimes > 0) {
		setTimeout(function() {
			for (const blink of document.querySelectorAll('.blink-once')) {
				blink.src = blink.src.includes(ledImgON) ? ledImgOFF : ledImgON
			}
			blinkOnce()
			blinktimes = blinktimes - 1
			// console.log(blinktimes)
		}, blinkInterval)
	}

	if (blinktimes <= 0) {
		console.log('end blink')
		setInterval(function() {
			for (const blink of document.querySelectorAll('.ledblink')) {
				blink.src = blink.src.includes(ledImgON) ? ledImgOFF : ledImgON
			}


		}, blinkInterval)

		updateLedsField1()
	}




}

blinkOnce()

function updateledField2() {
	getNewValueInField('field2', 2, function(data) {
		document.querySelector('.slider').value = data
		document.querySelector('.led-brightness').src = data <= 0 ? ledImgOFF : ledImgON
		
		document.querySelector('.range-value').innerHTML = Math.round( (data/1023)*100 ) 
	})


}
updateledField2()

document.querySelector('.slider').addEventListener('change', function(event) {
	controlsender('field2', event.target.value, function(temp) {})
	document.querySelector('.led-brightness').src = event.target.value <= 0 ? ledImgOFF : ledImgON
	document.querySelector('.range-value').innerHTML = Math.round( (event.target.value/1023)*100 )
})


for (const button of document.querySelectorAll('.led-ctrlall-btn')) button.addEventListener('click', function(event) {
	const isON = event.target.id == 'ON'

	controlsender('field1', isON ? 0xF : 0x0)
	//for (const led of document.querySelectorAll('.led-btn')) led.src = isON ? ledImgON : ledImgOFF
	for (const led of document.querySelectorAll('.led-control')) led.src = isON ? ledImgON : ledImgOFF
})




function makeRequest(method, url, callback) {
	let xhr = new XMLHttpRequest()
	xhr.open(method, url)
	xhr.onload = function() {
		if (this.status >= 200 && this.status < 300) {
			callback(xhr.responseText)
		}
	}
	xhr.send()
}
