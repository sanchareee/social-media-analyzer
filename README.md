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
    </style>
</head>
<body class="bg-gray-50 min-h-screen">
    <div class="container mx-auto px-4 py-12 max-w-4xl">
        <div class="bg-white rounded-xl shadow-md overflow-hidden p-6">
            <h1 class="text-3xl font-bold text-center text-indigo-700 mb-6">Instagram Profile Analyzer</h1>
            
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
                <p class="mt-2 text-sm text-gray-500">Note: This works best for public Instagram profiles.</p>
            </div>
            
            <div id="results" class="hidden">
                <!-- Profile Overview -->
                <div class="border-b border-gray-200 pb-6 mb-6">
                    <div class="flex items-center mb-4">
                        <img id="profilePic" src="" alt="Profile Picture" class="w-16 h-16 rounded-full object-cover mr-4 border border-gray-200" src="https://storage.googleapis.com/workspace-0f70711f-8b4e-4d94-86f1-2a93ccde5887/image/49e5b17b-f0b3-4cfa-8ae3-1d3634ac1fdd.png">
                        <div>
                            <h2 id="profileName" class="text-xl font-semibold"></h2>
                            <p id="profileUsername" class="text-gray-600"></p>
                        </div>
                    </div>
                    <div class="grid grid-cols-3 gap-4 text-center">
                        <div>
                            <p class="text-2xl font-bold" id="postCount">0</p>
                            <p class="text-sm text-gray-500">Posts</p>
                        </div>
                        <div>
                            <p class="text-2xl font-bold" id="followerCount">0</p>
                            <p class="text-sm text-gray-500">Followers</p>
                        </div>
                        <div>
                            <p class="text-2xl font-bold" id="followingCount">0</p>
                            <p class="text-sm text-gray-500">Following</p>
                        </div>
                    </div>
                </div>
                
                <!-- Content Breakdown -->
                <div class="mb-8">
                    <h3 class="text-lg font-semibold mb-4">Content Breakdown</h3>
                    <div class="grid grid-cols-2 gap-6 mb-8">
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
                    </div>
                </div>
                
                <!-- Engagement Potential -->
                <div class="bg-white border border-gray-200 rounded-lg p-4 mb-8">
                    <h4 class="font-medium mb-3">Engagement Potential</h4>
                    <div class="h-4 w-full bg-gray-200 rounded-full mb-2 overflow-hidden">
                        <div id="engagementBar" class="h-full bg-gradient-to-r from-indigo-400 to-purple-500 rounded-full" style="width: 0%"></div>
                    </div>
                    <p class="text-xs text-gray-500" id="engagementText">Reels typically have higher engagement than photos</p>
                </div>
                
                <!-- Monthly Metrics -->
                <div class="mb-8">
                    <h3 class="text-lg font-semibold mb-4">Monthly Metrics</h3>
                    <div class="bg-white border border-gray-200 rounded-lg p-4">
                        <div class="chart-container">
                            <canvas id="monthlyChart"></canvas>
                        </div>
                    </div>
                </div>
                
                <!-- Additional Metrics -->
                <div class="mb-8">
                    <h3 class="text-lg font-semibold mb-4">Additional Metrics</h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="bg-white border border-gray-200 rounded-lg p-4">
                            <h4 class="font-medium mb-2">Most Active Posting Times</h4>
                            <div id="activePostingTimes"></div>
                        </div>
                        <div class="bg-white border border-gray-200 rounded-lg p-4">
                            <h4 class="font-medium mb-2">Posts Per Week</h4>
                            <div id="postsPerWeek"></div>
                        </div>
                        <div class="bg-white border border-gray-200 rounded-lg p-4">
                            <h4 class="font-medium mb-2">Comment Reply Rate</h4>
                            <div id="commentReplyRate"></div>
                        </div>
                        <div class="bg-white border border-gray-200 rounded-lg p-4">
                            <h4 class="font-medium mb-2">External Link Clicks</h4>
                            <div id="externalLinkClicks"></div>
                        </div>
                    </div>
                </div>
            </div>
            
            <div id="error" class="hidden mt-4 p-4 bg-red-50 text-red-700 rounded-lg"></div>
        </div>
    </div>

    <script>
        // Global chart reference
        let monthlyChart = null;

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
                // Get mock data
                const profileData = getMockProfileData(username);
                const mediaCounts = getMockMediaCounts(username);
                const monthlyMetrics = getMockMonthlyMetrics(username);
                
                // Display results
                displayResults(profileData, mediaCounts, monthlyMetrics);
                document.getElementById('results').classList.remove('hidden');
            } catch (error) {
                console.error('Error:', error);
                showError('An error occurred. Please try again.');
            } finally {
                // Reset button
                document.getElementById('loadingSpinner').classList.add('hidden');
                document.getElementById('btnText').textContent = 'Analyze';
                document.getElementById('analyzeBtn').disabled = false;
            }
        }
        
        function getMockProfileData(username) {
            return {
                username: username,
                full_name: username === 'qua.nutrition' ? 'Qua Nutrition' : 'Test User',
                profile_pic_url: 'https://www.pngkey.com/png/detail/230-2301779_avatar-symbol-for-instagram.png',
                follower_count: username === 'qua.nutrition' ? 21000 : 5000,
                following_count: username === 'qua.nutrition' ? 480 : 200,
                posts_count: username === 'qua.nutrition' ? 2629 : 500
            };
        }
        
        function getMockMediaCounts(username) {
            return {
                reels: username === 'qua.nutrition' ? 350 : 50,
                photos: username === 'qua.nutrition' ? 2279 : 450,
                carousels: username === 'qua.nutrition' ? 100 : 20,
                stories: username === 'qua.nutrition' ? 800 : 150
            };
        }
        
        function getMockMonthlyMetrics(username) {
            const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
            const base = username === 'qua.nutrition' ? 100 : 20;
            
            return {
                months,
                followers: months.map((_, i) => base * 10 + Math.floor(Math.random() * base * 2) + i * 10),
                interactions: months.map((_, i) => base * 5 + Math.floor(Math.random() * base) + i * 5),
                views: months.map((_, i) => base * 20 + Math.floor(Math.random() * base * 4) + i * 15),
                activePostingTimes: ['Morning (8-11am)', 'Afternoon (1-4pm)', 'Evening (6-9pm)'],
                postsPerWeek: username === 'qua.nutrition' ? 7 : 3,
                commentReplyRate: username === 'qua.nutrition' ? '75%' : '50%',
                externalLinkClicks: username === 'qua.nutrition' ? '1,200' : '300'
            };
        }
        
        function displayResults(profileData, mediaCounts, monthlyMetrics) {
            // Profile info
            document.getElementById('profilePic').src = profileData.profile_pic_url;
            document.getElementById('profileName').textContent = profileData.full_name;
            document.getElementById('profileUsername').textContent = `@${profileData.username}`;
            
            // Basic counts
            document.getElementById('postCount').textContent = profileData.posts_count.toLocaleString();
            document.getElementById('followerCount').textContent = profileData.follower_count.toLocaleString();
            document.getElementById('followingCount').textContent = profileData.following_count.toLocaleString();
            
            // Content breakdown
            const totalPosts = mediaCounts.reels + mediaCounts.photos;
            document.getElementById('reelCount').textContent = mediaCounts.reels.toLocaleString();
            document.getElementById('photoCount').textContent = mediaCounts.photos.toLocaleString();
            
            const reelPercentage = Math.round((mediaCounts.reels / totalPosts) * 100);
            const photoPercentage = 100 - reelPercentage;
            document.getElementById('reelPercentage').textContent = `${reelPercentage}% of content`;
            document.getElementById('photoPercentage').textContent = `${photoPercentage}% of content`;
            
            // Engagement potential
            const engagementScore = Math.min(100, Math.round((reelPercentage * 0.7) + (photoPercentage * 0.3)));
            document.getElementById('engagementBar').style.width = `${engagementScore}%`;
            
            let engagementText = reelPercentage > 30 
                ? 'Great! This account posts many reels which typically get higher engagement.' 
                : reelPercentage > 15 
                    ? 'Good balance of reels and photos. Could increase reels for more engagement.' 
                    : 'Consider posting more reels to increase engagement potential.';
            document.getElementById('engagementText').textContent = engagementText;
            
            // Render charts
            renderMonthlyChart(monthlyMetrics);
            
            // Additional metrics
            document.getElementById('activePostingTimes').textContent = monthlyMetrics.activePostingTimes.join(', ');
            document.getElementById('postsPerWeek').textContent = `${monthlyMetrics.postsPerWeek} posts per week`;
            document.getElementById('commentReplyRate').textContent = monthlyMetrics.commentReplyRate;
            document.getElementById('externalLinkClicks').textContent = `${monthlyMetrics.externalLinkClicks} clicks`;
        }
        
        function renderMonthlyChart(metrics) {
            const ctx = document.getElementById('monthlyChart').getContext('2d');
            
            // Destroy previous chart if it exists
            if (monthlyChart) {
                monthlyChart.destroy();
            }
            
            monthlyChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: metrics.months,
                    datasets: [
                        {
                            label: 'Followers',
                            data: metrics.followers,
                            backgroundColor: 'rgba(79, 70, 229, 0.7)',
                            borderColor: 'rgba(79, 70, 229, 1)',
                            borderWidth: 1
                        },
                        {
                            label: 'Interactions',
                            data: metrics.interactions,
                            backgroundColor: 'rgba(99, 102, 241, 0.7)',
                            borderColor: 'rgba(99, 102, 241, 1)',
                            borderWidth: 1
                        },
                        {
                            label: 'Views',
                            data: metrics.views,
                            backgroundColor: 'rgba(129, 140, 248, 0.7)',
                            borderColor: 'rgba(129, 140, 248, 1)',
                            borderWidth: 1
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true
                        }
                    }
                }
            });
        }
        
        function showError(message) {
            const errorDiv = document.getElementById('error');
            errorDiv.textContent = message;
            errorDiv.classList.remove('hidden');
        }
    </script>
</body>
</html>
