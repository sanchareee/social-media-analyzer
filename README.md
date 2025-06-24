<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Instagram Profile Analyzer</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        .loading-spinner {
            border: 4px solid rgba(0, 0, 0, 0.1);
            border-radius: 50%;
            border-top: 4px solid #3498db;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .chart-container {
            position: relative;
            height: 300px;
        }
        .post-type-badge {
            display: inline-block;
            padding: 2px 8px;
            border-radius: 12px;
            font-size: 12px;
            font-weight: 500;
        }
        .day-column {
            min-width: 120px;
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">
    <div class="container mx-auto px-4 py-12 max-w-6xl">
        <div class="bg-white rounded-xl shadow-md overflow-hidden p-6">
            <h1 class="text-3xl font-bold text-center text-indigo-700 mb-6">Advanced Instagram Profile Analyzer</h1>
            
            <div class="mb-8">
                <label for="username" class="block text-sm font-medium text-gray-700 mb-2">
                    Enter Instagram Username
                </label>
                <div class="flex gap-2">
                    <input 
                        type="text" 
                        id="username" 
                        placeholder="e.g., qua.nutrition" 
                        class="flex-1 px-4 py-2 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500"
                    >
                    <button 
                        onclick="analyzeProfile()" 
                        id="analyzeBtn"
                        class="px-6 py-2 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 transition-colors flex items-center justify-center"
                    >
                        <span id="btnText">Analyze</span>
                        <div id="loadingSpinner" class="loading-spinner ml-2 hidden"></div>
                    </button>
                </div>
                <p class="mt-2 text-sm text-gray-500">Works for public Instagram profiles. Example: qua.nutrition</p>
            </div>
            
            <div id="results" class="hidden space-y-8">
                <!-- Profile Overview -->
                <div class="border-b border-gray-200 pb-6">
                    <div class="flex items-center mb-4">
                        <img id="profilePic" src="" alt="Profile Picture" class="w-16 h-16 rounded-full object-cover mr-4 border border-gray-200">
                        <div>
                            <h2 id="profileName" class="text-xl font-semibold"></h2>
                            <p id="profileUsername" class="text-gray-600"></p>
                        </div>
                    </div>
                    <div class="grid grid-cols-4 gap-4 text-center">
                        <div>
                            <p class="text-2xl font-bold" id="postCount">0</p>
                            <p class="text-sm text-gray-500">Total Posts</p>
                        </div>
                        <div>
                            <p class="text-2xl font-bold" id="followerCount">0</p>
                            <p class="text-sm text-gray-500">Followers</p>
                        </div>
                        <div>
                            <p class="text-2xl font-bold" id="followingCount">0</p>
                            <p class="text-sm text-gray-500">Following</p>
                        </div>
                        <div>
                            <p class="text-2xl font-bold" id="engagementRate">0%</p>
                            <p class="text-sm text-gray-500">Engagement Rate</p>
                        </div>
                    </div>
                </div>

                <!-- Content Breakdown -->
                <div>
                    <h3 class="text-xl font-semibold mb-4 text-indigo-700">Content Composition</h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-6">
                        <div class="bg-indigo-50 p-4 rounded-lg">
                            <p class="text-3xl font-bold text-indigo-700 mb-2" id="reelCount">0</p>
                            <p class="text-gray-600">Reels</p>
                            <p class="text-xs text-gray-500 mt-2" id="reelPercentage">0% of content</p>
                        </div>
                        <div class="bg-purple-50 p-4 rounded-lg">
                            <p class="text-3xl font-bold text-purple-700 mb-2" id="photoCount">0</p>
                            <p class="text-gray-600">Photos</p>
                            <p class="text-xs text-gray-500 mt-2" id="photoPercentage">0% of content</p>
                        </div>
                        <div class="bg-blue-50 p-4 rounded-lg">
                            <p class="text-3xl font-bold text-blue-700 mb-2" id="carouselCount">0</p>
                            <p class="text-gray-600">Carousels</p>
                            <p class="text-xs text-gray-500 mt-2" id="carouselPercentage">0% of content</p>
                        </div>
                        <div class="bg-green-50 p-4 rounded-lg">
                            <p class="text-3xl font-bold text-green-700 mb-2" id="storyCount">0</p>
                            <p class="text-gray-600">Stories</p>
                            <p class="text-xs text-gray-500 mt-2" id="storyPercentage">0% of content</p>
                        </div>
                    </div>
                    <div class="bg-white border border-gray-200 rounded-lg p-4">
                        <div class="chart-container">
                            <canvas id="contentTypeChart"></canvas>
                        </div>
                    </div>
                </div>

                <!-- Monthly Breakdown -->
                <div>
                    <h3 class="text-xl font-semibold mb-4 text-indigo-700">Monthly Posting Activity</h3>
                    <div class="mb-6 bg-white border border-gray-200 rounded-lg p-4">
                        <div class="chart-container">
                            <canvas id="monthlyTrendChart"></canvas>
                        </div>
                    </div>
                    
                    <div class="overflow-x-auto">
                        <table class="min-w-full bg-white border border-gray-200 rounded-lg">
                            <thead class="bg-gray-50">
                                <tr>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Month</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Reels</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Photos</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Carousels</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Stories</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Posts/Week</th>
                                </tr>
                            </thead>
                            <tbody id="monthlyBreakdown" class="divide-y divide-gray-200"></tbody>
                        </table>
                    </div>
                </div>

                <!-- Daily Posting Patterns -->
                <div>
                    <h3 class="text-xl font-semibold mb-4 text-indigo-700">Daily Posting Patterns</h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div class="bg-white border border-gray-200 rounded-lg p-4">
                            <h4 class="font-medium mb-3">Posts by Day of Week</h4>
                            <div class="chart-container">
                                <canvas id="dailyPatternChart"></canvas>
                            </div>
                        </div>
                        <div class="bg-white border border-gray-200 rounded-lg p-4">
                            <h4 class="font-medium mb-3">Posting Times</h4>
                            <div class="chart-container">
                                <canvas id="postingTimesChart"></canvas>
                            </div>
                        </div>
                    </div>
                    
                    <div class="mt-6 bg-white border border-gray-200 rounded-lg p-4">
                        <h4 class="font-medium mb-3">Recent Posts Timeline</h4>
                        <div id="timeline" class="space-y-4"></div>
                    </div>
                </div>
            </div>
            
            <div id="error" class="hidden mt-4 p-4 bg-red-50 text-red-700 rounded-lg"></div>
        </div>
    </div>

    <script>
        // Chart references
        let contentTypeChart, monthlyTrendChart, dailyPatternChart, postingTimesChart;

        async function analyzeProfile() {
            const username = document.getElementById('username').value.trim();
            if (!username) {
                showError('Please enter an Instagram username');
                return;
            }
            
            // Show loading state
            document.getElementById('loadingSpinner').classList.remove('hidden');
            document.getElementById('btnText').textContent = 'Analyzing';
            document.getElementById('analyzeBtn').disabled = true;
            document.getElementById('error').classList.add('hidden');
            
            try {
                const profileData = await fetchMockProfileData(username);
                const analyticsData = generateMockAnalyticsData(username);
                
                displayProfileData(profileData);
                renderCharts(analyticsData);
                displayMonthlyBreakdown(analyticsData);
                displayTimeline(analyticsData.recentPosts);
                
                document.getElementById('results').classList.remove('hidden');
            } catch (error) {
                console.error('Analysis error:', error);
                showError('Failed to analyze profile. Please try again.');
            } finally {
                document.getElementById('loadingSpinner').classList.add('hidden');
                document.getElementById('btnText').textContent = 'Analyze';
                document.getElementById('analyzeBtn').disabled = false;
            }
        }
        
        function fetchMockProfileData(username) {
            // Simulate API delay
            return new Promise(resolve => {
                setTimeout(() => {
                    const isDemoAccount = username === 'qua.nutrition';
                    resolve({
                        username: username,
                        full_name: isDemoAccount ? 'Qua Nutrition' : 
                                  username.split('.')[0].charAt(0).toUpperCase() + username.split('.')[0].slice(1),
                        profile_pic_url: 'https://www.pngkey.com/png/detail/230-2301779_avatar-symbol-for-instagram.png',
                        follower_count: isDemoAccount ? 21000 : Math.floor(Math.random() * 50000) + 1000,
                        following_count: isDemoAccount ? 480 : Math.floor(Math.random() * 500) + 50,
                        post_count: isDemoAccount ? 2629 : Math.floor(Math.random() * 2000) + 100
                    });
                }, 800);
            });
        }
        
        function generateMockAnalyticsData(username) {
            const isDemoAccount = username === 'qua.nutrition';
            const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
            const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
            const hours = Array.from({length: 24}, (_, i) => i);
            
            // Generate monthly data
            const monthlyData = months.map((month, i) => {
                const base = isDemoAccount ? 30 : Math.floor(Math.random() * 15) + 5;
                return {
                    month,
                    reels: base + Math.floor(Math.random() * 10),
                    photos: base * 2 + Math.floor(Math.random() * 15),
                    carousels: base / 2 + Math.floor(Math.random() * 8),
                    stories: base * 3 + Math.floor(Math.random() * 20),
                    postsPerWeek: Math.floor(Math.random() * 7) + 2
                };
            });
            
            // Generate daily pattern data
            const dailyPattern = days.map(day => ({
                day,
                count: Math.floor(Math.random() * 15) + 5
            }));
            
            // Generate posting times data
            const postingTimes = hours.map(hour => ({
                hour,
                count: Math.floor(Math.max(0, 10 * Math.sin(hour / 4) + 5 + Math.random() * 3))
            }));
            
            // Generate recent posts for timeline
            const recentPosts = Array.from({length: 7}, (_, i) => {
                const daysAgo = i;
                const types = ['reel', 'photo', 'carousel', 'story'];
                const type = types[Math.floor(Math.random() * types.length)];
                const hour = 7 + Math.floor(Math.random() * 12);
                const time = `${hour}:${Math.floor(Math.random() * 60).toString().padStart(2, '0')}`;
                
                return {
                    daysAgo,
                    type,
                    time,
                    content: `Sample ${type} posted ${daysAgo} day${daysAgo !== 1 ? 's' : ''} ago at ${time}`
                };
            });
            
            return {
                months,
                monthlyData,
                dailyPattern,
                postingTimes,
                recentPosts
            };
        }
        
        function displayProfileData(profile) {
            document.getElementById('profilePic').src = profile.profile_pic_url;
            document.getElementById('profileName').textContent = profile.full_name;
            document.getElementById('profileUsername').textContent = `@${profile.username}`;
            
            document.getElementById('postCount').textContent = profile.post_count.toLocaleString();
            document.getElementById('followerCount').textContent = profile.follower_count.toLocaleString();
            document.getElementById('followingCount').textContent = profile.following_count.toLocaleString();
            
            // Calculate mock engagement rate (1-5%)
            const engagementRate = (Math.random() * 4 + 1).toFixed(1);
            document.getElementById('engagementRate').textContent = `${engagementRate}%`;
        }
        
        function renderCharts(data) {
            // Destroy existing charts
            if (contentTypeChart) contentTypeChart.destroy();
            if (monthlyTrendChart) monthlyTrendChart.destroy();
            if (dailyPatternChart) dailyPatternChart.destroy();
            if (postingTimesChart) postingTimesChart.destroy();
            
            // Content Type Pie Chart
            const contentTypeCtx = document.getElementById('contentTypeChart').getContext('2d');
            contentTypeChart = new Chart(contentTypeCtx, {
                type: 'pie',
                data: {
                    labels: ['Reels', 'Photos', 'Carousels', 'Stories'],
                    datasets: [{
                        data: [45, 30, 15, 60],
                        backgroundColor: [
                            'rgba(79, 70, 229, 0.7)',
                            'rgba(124, 58, 237, 0.7)',
                            'rgba(59, 130, 246, 0.7)',
                            'rgba(16, 185, 129, 0.7)'
                        ],
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: { position: 'right' }
                    }
                }
            });
            
            // Monthly Trend Chart
            const monthlyCtx = document.getElementById('monthlyTrendChart').getContext('2d');
            monthlyTrendChart = new Chart(monthlyCtx, {
                type: 'line',
                data: {
                    labels: data.months,
                    datasets: [
                        {
                            label: 'Reels',
                            data: data.monthlyData.map(m => m.reels),
                            borderColor: 'rgba(79, 70, 229, 1)',
                            backgroundColor: 'rgba(79, 70, 229, 0.1)',
                            tension: 0.3
                        },
                        {
                            label: 'Photos',
                            data: data.monthlyData.map(m => m.photos),
                            borderColor: 'rgba(124, 58, 237, 1)',
                            backgroundColor: 'rgba(124, 58, 237, 0.1)',
                            tension: 0.3
                        },
                        {
                            label: 'Carousels',
                            data: data.monthlyData.map(m => m.carousels),
                            borderColor: 'rgba(59, 130, 246, 1)',
                            backgroundColor: 'rgba(59, 130, 246, 0.1)',
                            tension: 0.3
                        },
                        {
                            label: 'Stories',
                            data: data.monthlyData.map(m => m.stories),
                            borderColor: 'rgba(16, 185, 129, 1)',
                            backgroundColor: 'rgba(16, 185, 129, 0.1)',
                            tension: 0.3
                        }
                    ]
                },
                options: {
                    responsive: true,
                    scales: {
                        y: { beginAtZero: true }
                    }
                }
            });
            
            // Daily Pattern Chart
            const dailyCtx = document.getElementById('dailyPatternChart').getContext('2d');
            dailyPatternChart = new Chart(dailyCtx, {
                type: 'bar',
                data: {
                    labels: data.dailyPattern.map(d => d.day),
                    datasets: [



