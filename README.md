<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Can Güngör Binance Güncel Pozisyonlar</title>

    <link href="https://fonts.googleapis.com/css2?family=Rosemary&display=swap" rel="stylesheet">

    <style>

        body {

            font-family: 'Rosemary', Arial, sans-serif;

            margin: 20px;

        }

        h1 {

            text-align: center; /* Başlığı ortala */

        }

        table {

            width: 100%;

            border-collapse: collapse;

        }

        th, td {

            border: 1px solid #ddd;

            padding: 8px;

            text-align: center;

        }

        th {

            background-color: #f4f4f4;

        }

        tr.long {

            background-color: #d4edda; /* Yeşil ton */

        }

        tr.short {

            background-color: #f8d7da; /* Kırmızı ton */

        }

    </style>

</head>

<body>

    <h1>Can Güngör Binance Güncel Pozisyonlar</h1>

    <table id="positionsTable">

        <thead>

            <tr>

                <th>Coin</th>

                <th>Giriş Fiyatı</th>

                <th>Pozisyon</th>

                <th>Kaldıraç</th> <!-- Kaldıraç sütunu eklendi -->

                <th>Liq Fiyatı</th>

                <th>ROI (%)</th>

            </tr>

        </thead>

        <tbody>

            <!-- Dinamik veriler buraya eklenecek -->

        </tbody>

    </table>



    <script>

        const API_KEY = "hKyfs6KpoweZbSxoJ3yb9aC5rIhhjgS31gqicfpXur02NaRTdGM2Gk8S5Z7uedMg";

        const API_SECRET = "hMOmJoXNUVqmInQkL4tDnKM3pW2tFmT0sMkrtBGYtwyhcKZ3yMJgbffEcTvYrViG";



        const BASE_URL = "https://fapi.binance.com";



        function createSignature(queryString, secret) {

            return CryptoJS.HmacSHA256(queryString, secret).toString(CryptoJS.enc.Hex);

        }



        async function fetchPositions() {

            try {

                const timestamp = Date.now();

                const queryString = `timestamp=${timestamp}`;

                const signature = createSignature(queryString, API_SECRET);



                const response = await fetch(`${BASE_URL}/fapi/v2/positionRisk?${queryString}&signature=${signature}`, {

                    method: "GET",

                    headers: {

                        "X-MBX-APIKEY": API_KEY

                    }

                });



                const data = await response.json();

                if (response.ok) {

                    const openPositions = data.filter(pos => parseFloat(pos.positionAmt) !== 0);

                    populateTable(openPositions);

                } else {

                    console.error("API Error:", data);

                }

            } catch (error) {

                console.error("Fetch Error:", error);

            }

        }



        function calculateROI(entryPrice, positionAmt, unrealizedProfit) {

            const totalInvestment = entryPrice * positionAmt;

            if (totalInvestment === 0) return 0;

            return (unrealizedProfit / totalInvestment) * 100;

        }



        function populateTable(positions) {

            const tableBody = document.querySelector("#positionsTable tbody");

            tableBody.innerHTML = "";



            positions.forEach(pos => {

                const row = document.createElement("tr");



                // Pozisyona göre sınıf ekle

                if (parseFloat(pos.positionAmt) > 0) {

                    row.classList.add("long");

                } else {

                    row.classList.add("short");

                }



                const roi = calculateROI(parseFloat(pos.entryPrice), parseFloat(pos.positionAmt), parseFloat(pos.unRealizedProfit));

                const leverage = `${pos.leverage}x`; // Kaldıraç değerini "x" ile formatlayarak ekliyoruz.



                row.innerHTML = `

                    <td>${pos.symbol}</td>

                    <td>${parseFloat(pos.entryPrice).toFixed(2)}</td>

                    <td>${parseFloat(pos.positionAmt) > 0 ? "Long" : "Short"}</td>

                    <td>${leverage}</td> <!-- Kaldıraç formatında gösterildi -->

                    <td>${parseFloat(pos.liquidationPrice).toFixed(2)}</td>

                    <td>${roi.toFixed(2)}</td>

                `;

                tableBody.appendChild(row);

            });

        }



        setInterval(fetchPositions, 5000);

        fetchPositions();

    </script>



    <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>

</body>

</html>
