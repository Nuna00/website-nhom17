var Channell = 1421792
var Keydoc = 'DWTWKRWRYGQY0N0M'
var Keyghi = 'SSDLCZWNRHIVMJUF'

var Point = 20
var Timeup = 15000



/* tạo chart */
var ctx = document.getElementById('myChart').getContext('2d');
var chart = new Chart(ctx, {
	type: 'line',
	data: {
		labels: [],
		datasets: [{
			label: 'Dữ liệu biến trở',
			data: [],
			borderColor: '#99ff66',
			borderWidth: 3,
			showLine: true,
			color: 'white'
		}]
	},
	options: {
		maintainAspectRatio: false,
		responsive: true,
		scales: {
			x: {
				ticks: {
					color:  "#999999"
				},
				grid: {
					color: "#999999"
				}
			},
			y: {
				ticks: {
					color: "#999999"
				},
				grid: {
					color: "#999999"
				}
			}
		},
		/*options.plugins.legend.labels*/
		plugins: {
			legend: {
				labels: {
					color: "#999999",
					
				}
			}
		}

	},
})



function updateDate(length) {



	ReadNoNull('field3', 3, function(rawData) {



		if (rawData.length < length) {

			if (arrayCompare(chart.data.datasets[0].data, rawData) == false) {
				chart.data.labels.length = 0
				chart.data.datasets[0].data.length = 0

				chart.data.labels.push(...Array.from(Array(rawData.length).keys()))
				chart.data.datasets[0].data.push(...rawData)
			}
		} else {
			let newXArray = rawData.slice(rawData.length - length)

			if (arrayCompare(chart.data.datasets[0].data, newXArray) == false) {

				chart.data.labels.length = 0
				chart.data.datasets[0].data.length = 0

				chart.data.labels.push(...Array.from(Array(length).keys()))
				chart.data.datasets[0].data.push(...newXArray)
			}
		}

		chart.update()

	})

}

updateDate(Point)

setInterval(() => {
	updateDate(Point)
}, Timeup)


function arrayCompare(arr1, arr2) {

	if (arr1.length != arr2.length) { return false }

	let result = true

	arr1.forEach((arr1Element, idx) => {
		if (arr1Element != arr2[idx]) {
			result = false
		}
	})
	return result
}


function ReadNoNull(fieldName, fieldID, callback) {

	let url = 'https://api.thingspeak.com/channels/' + Channell + '/fields/' + fieldID + '/last.json?api_key=' + Keydoc

	makeRequest('GET', url, function(lastedJson) {
		const lastedEntryID = JSON.parse(lastedJson)['entry_id']

		url = 'https://api.thingspeak.com/channels/' + Channell + '/fields/' + fieldID + '.json?api_key=' + Keydoc + '&results=' + lastedEntryID

		makeRequest('GET', url, function(fieldJson) {
			const rawData = JSON.parse(fieldJson)['feeds']

			const data = []
			for (const rawdata of rawData) {
				if (rawdata[fieldName] != null)
					data.push(parseInt(rawdata[fieldName]))
			}

			callback(data)

		})

	})

}

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
