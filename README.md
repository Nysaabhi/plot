<!DOCTYPE html>
<html>
<head>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Arial, sans-serif;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 30px;
        }

        .header {
            margin-bottom: 30px;
        }

        .search-section {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 30px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }

        .search-container {
            display: flex;
            gap: 15px;
            margin-bottom: 20px;
        }

        .search-bar {
            flex: 1;
            padding: 12px 20px;
            border: 2px solid #e1e4e8;
            border-radius: 8px;
            font-size: 16px;
            transition: all 0.3s;
        }

        .search-bar:focus {
            outline: none;
            border-color: #4CAF50;
            box-shadow: 0 0 0 3px rgba(76, 175, 80, 0.1);
        }

        .filters {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
        }

        select {
            width: 100%;
            padding: 10px;
            border: 2px solid #e1e4e8;
            border-radius: 8px;
            background: white;
            cursor: pointer;
        }

        .plot-grid {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            gap: 15px;
            margin-bottom: 30px;
        }

        .plot {
            aspect-ratio: 1;
            border: 2px solid #e1e4e8;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 10px;
            position: relative;
        }

        .plot:hover {
            transform: translateY(-5px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        .plot-number {
            font-weight: 600;
            font-size: 1.1em;
            margin-bottom: 5px;
        }

        .plot-size {
            font-size: 0.9em;
            color: #666;
        }

        .plot-status {
            position: absolute;
            top: 8px;
            right: 8px;
            width: 8px;
            height: 8px;
            border-radius: 50%;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
            backdrop-filter: blur(5px);
        }

        .modal-content {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: white;
            padding: 30px;
            border-radius: 12px;
            width: 90%;
            max-width: 700px;
            max-height: 90vh;
            overflow-y: auto;
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 25px;
            padding-bottom: 15px;
            border-bottom: 2px solid #f0f0f0;
        }

        .close-modal {
            cursor: pointer;
            font-size: 24px;
            color: #666;
        }

        .plot-details {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
            margin-bottom: 25px;
        }

        .detail-item {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 8px;
        }

        .detail-label {
            color: #666;
            margin-bottom: 5px;
            font-size: 0.9em;
        }

        .detail-value {
            font-weight: 600;
            font-size: 1.1em;
        }

        .action-buttons {
            display: flex;
            gap: 15px;
            margin-top: 25px;
        }

        .btn {
            flex: 1;
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s;
            text-align: center;
        }

        .btn-primary {
            background: #4CAF50;
            color: white;
        }

        .btn-primary:hover {
            background: #45a049;
        }

        .btn-secondary {
            background: #f8f9fa;
            color: #333;
            border: 2px solid #e1e4e8;
        }

        .btn-secondary:hover {
            background: #e9ecef;
        }

        .legend {
            display: flex;
            gap: 20px;
            padding: 15px;
            background: #f8f9fa;
            border-radius: 8px;
            margin-bottom: 20px;
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .legend-color {
            width: 12px;
            height: 12px;
            border-radius: 50%;
        }

        @media (max-width: 768px) {
            .plot-grid {
                grid-template-columns: repeat(4, 1fr);
            }
            
            .plot-details {
                grid-template-columns: 1fr;
            }
            
            .action-buttons {
                flex-direction: column;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Plot Map</h1>
        </div>

        <div class="search-section">
            <div class="search-container">
                <input type="text" class="search-bar" placeholder="Search by plot number, size, or location...">
            </div>
            <div class="filters">
                <select id="phase-filter">
                    <option value="">Select Phase</option>
                    <option value="phase-a">Phase A</option>
                    <option value="phase-b">Phase B</option>
                    <option value="phase-c">Phase C</option>
                </select>

                <select id="status-filter">
                    <option value="">Plot Status</option>
                    <option value="available">Available</option>
                    <option value="sold">Sold</option>
                    <option value="reserved">Reserved</option>
                </select>

                <select id="size-filter">
                    <option value="">Plot Size</option>
                    <option value="small">Small (< 1000 sq ft)</option>
                    <option value="medium">Medium (1000-2000 sq ft)</option>
                    <option value="large">Large (> 2000 sq ft)</option>
                </select>

                <select id="price-filter">
                    <option value="">Price Range</option>
                    <option value="0-500000">Under $500,000</option>
                    <option value="500000-1000000">$500,000 - $1,000,000</option>
                    <option value="1000000+">Above $1,000,000</option>
                </select>
            </div>
        </div>

        <div class="legend">
            <div class="legend-item">
                <div class="legend-color" style="background: #4CAF50;"></div>
                <span>Available</span>
            </div>
            <div class="legend-item">
                <div class="legend-color" style="background: #f44336;"></div>
                <span>Sold</span>
            </div>
            <div class="legend-item">
                <div class="legend-color" style="background: #FFC107;"></div>
                <span>Reserved</span>
            </div>
        </div>

        <div id="plot-grid" class="plot-grid">
            <!-- Plots will be generated here -->
        </div>
    </div>

    <div id="plot-modal" class="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Plot Details</h2>
                <span class="close-modal">&times;</span>
            </div>
            <div class="plot-details">
                <!-- Details will be populated via JavaScript -->
            </div>
            <div class="action-buttons">
                <button class="btn btn-primary" onclick="handleBooking()">Book Now</button>
                <button class="btn btn-secondary" onclick="scheduleVisit()">Schedule Visit</button>
                <button class="btn btn-secondary" onclick="downloadBrochure()">Download Brochure</button>
            </div>
        </div>
    </div>

    <script>
        // Generate plot data
        const generatePlots = () => {
            const grid = document.getElementById('plot-grid');
            const statuses = ['available', 'sold', 'reserved'];
            const statusColors = {
                'available': '#4CAF50',
                'sold': '#f44336',
                'reserved': '#FFC107'
            };

            for (let i = 1; i <= 32; i++) {
                const status = statuses[Math.floor(Math.random() * statuses.length)];
                const plot = document.createElement('div');
                plot.className = 'plot';
                plot.innerHTML = `
                    <div class="plot-status" style="background: ${statusColors[status]}"></div>
                    <div class="plot-number">Plot A${i}</div>
                    <div class="plot-size">${800 + Math.floor(Math.random() * 1200)} sq ft</div>
                `;
                plot.addEventListener('click', () => showPlotDetails(i));
                grid.appendChild(plot);
            }
        };

        const showPlotDetails = (plotId) => {
            const modal = document.getElementById('plot-modal');
            const detailsContainer = modal.querySelector('.plot-details');
            
            const details = {
                'Plot Number': `Plot A${plotId}`,
                'Size': `${800 + Math.floor(Math.random() * 1200)} sq ft`,
                'Price': `$${(300000 + Math.floor(Math.random() * 700000)).toLocaleString()}`,
                'Status': ['Available', 'Sold', 'Reserved'][Math.floor(Math.random() * 3)],
                'Location': 'Phase A, Block 2',
                'Direction': ['North', 'South', 'East', 'West'][Math.floor(Math.random() * 4)] + ' Facing',
                'Dimensions': '40 x 80 ft',
                'Development Status': `${Math.floor(Math.random() * 100)}% Complete`
            };

            detailsContainer.innerHTML = Object.entries(details)
                .map(([label, value]) => `
                    <div class="detail-item">
                        <div class="detail-label">${label}</div>
                        <div class="detail-value">${value}</div>
                    </div>
                `).join('');

            modal.style.display = 'block';
        };

        // Action button handlers
        const handleBooking = () => alert('Redirecting to booking form...');
        const scheduleVisit = () => alert('Opening visit scheduler...');
        const downloadBrochure = () => alert('Downloading plot brochure...');

        // Close modal
        document.querySelector('.close-modal').onclick = () => {
            document.getElementById('plot-modal').style.display = 'none';
        };

        window.onclick = (event) => {
            const modal = document.getElementById('plot-modal');
            if (event.target === modal) {
                modal.style.display = 'none';
            }
        };

        // Search functionality
        document.querySelector('.search-bar').addEventListener('input', (e) => {
            const searchTerm = e.target.value.toLowerCase();
            document.querySelectorAll('.plot').forEach(plot => {
                const plotText = plot.textContent.toLowerCase();
                plot.style.display = plotText.includes(searchTerm) ? 'flex' : 'none';
            });
        });

        // Initialize
        generatePlots();
    </script>
</body>
</html>
