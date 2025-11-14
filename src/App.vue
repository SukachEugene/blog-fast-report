<script setup>
import { ref, onMounted, computed } from 'vue';

const API_URL = 'https://igaming.mydigicode.com/api';
const posts = ref([]);
const error = ref(null);
const parsedResults = ref([]);

// Sorting state
const sortBy = ref('date'); // 'date', 'keywords', 'matches'
const sortOrder = ref('desc'); // 'asc', 'desc'

// Section collapse state
const sectionsExpanded = ref({
    keywords: false,
    statistics: false,
    results: true
});

const toggleSection = (section) => {
    sectionsExpanded.value[section] = !sectionsExpanded.value[section];
};

// List of keywords to search for 
// (from: https://docs.google.com/document/d/1QYqS1It-xEDbHXuWU5cCgeptvLNhoEzhGGeubi86unI/edit?tab=t.0)
// const searchKeywords = ref([
//     'casino',
//     'online casino',
//     'slots',
//     'slot machine',
//     'slot game',
//     'poker',
//     'roulette',
//     'blackjack',
//     'baccarat',
//     'gambling',
//     'betting',
//     'bet',
//     'sportsbook',
//     'bookmaker',
//     'wager',
//     'wagering',
//     'bingo',
//     'lotto',
//     'lottery',
//     'jackpot',
//     'live dealer',
//     'live casino',
//     'spin',
//     'spin to win',
//     'real money',
//     'win money',
//     'win cash',
//     'cash prize',
//     'payout',
//     'withdraw',
//     'withdrawal',
//     'deposit',
//     'deposit bonus',
//     'no deposit bonus',
//     'instant payout',
//     'fast withdrawal',
//     'earn money',
//     'make money fast',
//     'real winnings',
//     'real rewards',
//     'big win',
//     'huge jackpot',
//     'risk-free',
//     'guaranteed profit',
//     'bet now',
//     'play now',
//     'start playing',
//     'real cash',
//     'high RTP',
//     'best odds',
//     'odds',
//     'stake',
//     'return to player',
//     'casino bonus',
//     'welcome bonus',
//     'bonus code',
//     'free spins',
//     'free play',
//     'free bet',
//     'jackpot slot',
//     'progressive jackpot',
//     'daily jackpot',
//     'crypto casino',
//     'bitcoin casino',
//     'gambling site',
//     'online betting',
//     'sports betting',
//     'betting site',
//     'betting platform',
//     'betting odds',
//     'betting tips',
//     'betting strategy',
//     'place your bet',
//     'cashback offer',
//     'real player',
//     'real dealer',
//     'real gameplay',
//     'VIP rewards',
//     'high roller',
//     'casino rewards',
//     'wager requirement',
//     'low wagering',
//     'minimum deposit',
//     'maximum payout',
//     'instant win',
//     'scratch card',
//     'double your money',
//     'risk-free bet',
//     'casino app',
//     'mobile casino',
//     'play for cash',
//     'spin and win',
//     'no wagering',
//     'real-time betting',
//     'sports odds',
//     'betting bonus',
//     'money back',
//     'deposit match',
//     'stake bonus',
//     'fast cashout',
//     'withdraw instantly',
//     'win big',
//     'earn big',
//     'casino jackpot',
//     'free chips',
//     'online slots bonus',
//     'gambling rewards',
//     'poker tournament',
//     'casino tournament',
//     'payout ratio',
//     'crypto betting',
//     'blockchain casino',
//     'NFT casino',
//     'provably fair'
// ]);

// expanded list with plura
const searchKeywords = ref([
    'casino', 'casinos',
    'online casino', 'online casinos',
    'slots',
    'slot machine', 'slot machines',
    'slot game', 'slot games',
    'poker',
    'roulette',
    'blackjack',
    'baccarat',
    'gambling',
    'betting',
    'bet', 'bets',
    'sportsbook', 'sportsbooks',
    'bookmaker', 'bookmakers',
    'wager', 'wagers',
    'wagering',
    'bingo',
    'lotto', 'lottos',
    'lottery', 'lotteries',
    'jackpot', 'jackpots',
    'live dealer', 'live dealers',
    'live casino', 'live casinos',
    'spin', 'spins',
    'spin to win',
    'real money',
    'win money',
    'win cash',
    'cash prize', 'cash prizes',
    'payout', 'payouts',
    'withdraw',
    'withdrawal', 'withdrawals',
    'deposit', 'deposits',
    'deposit bonus', 'deposit bonuses',
    'no deposit bonus', 'no deposit bonuses',
    'instant payout', 'instant payouts',
    'fast withdrawal', 'fast withdrawals',
    'earn money',
    'make money fast',
    'real winnings',
    'real rewards',
    'big win', 'big wins',
    'huge jackpot', 'huge jackpots',
    'risk-free',
    'guaranteed profit',
    'bet now',
    'play now',
    'start playing',
    'real cash',
    'high RTP',
    'best odds', 
    'odds',
    'stake', 'stakes',
    'return to player',
    'casino bonus', 'casino bonuses',
    'welcome bonus', 'welcome bonuses',
    'bonus code', 'bonus codes',
    'free spins',
    'free play',
    'free bet', 'free bets',
    'jackpot slot', 'jackpot slots',
    'progressive jackpot', 'progressive jackpots',
    'daily jackpot', 'daily jackpots',
    'crypto casino', 'crypto casinos',
    'bitcoin casino', 'bitcoin casinos',
    'gambling site', 'gambling sites',
    'online betting',
    'sports betting',
    'betting site', 'betting sites',
    'betting platform', 'betting platforms',
    'betting odds',
    'betting tips',
    'betting strategy', 'betting strategies',
    'place your bet',
    'cashback offer', 'cashback offers',
    'real player', 'real players',
    'real dealer', 'real dealers',
    'real gameplay',
    'VIP rewards',
    'high roller', 'high rollers',
    'casino rewards',
    'wager requirement', 'wager requirements',
    'low wagering',
    'minimum deposit', 'minimum deposits',
    'maximum payout', 'maximum payouts',
    'instant win', 'instant wins',
    'scratch card', 'scratch cards',
    'double your money',
    'risk-free bet', 'risk-free bets',
    'casino app', 'casino apps',
    'mobile casino', 'mobile casinos',
    'play for cash',
    'spin and win',
    'no wagering',
    'real-time betting',
    'sports odds',
    'betting bonus', 'betting bonuses',
    'money back',
    'deposit match', 'deposit matches',
    'stake bonus', 'stake bonuses',
    'fast cashout', 'fast cashouts',
    'withdraw instantly',
    'win big',
    'earn big',
    'casino jackpot', 'casino jackpots',
    'free chips',
    'online slots bonus', 'online slots bonuses',
    'gambling rewards',
    'poker tournament', 'poker tournaments',
    'casino tournament', 'casino tournaments',
    'payout ratio', 'payout ratios',
    'crypto betting',
    'blockchain casino', 'blockchain casinos',
    'NFT casino', 'NFT casinos',
    'provably fair'
]);

const fetchPosts = async () => {
    error.value = null;

    try {
        const response = await fetch(`${API_URL}/posts?&populate[FAQ][populate]=*&pagination[pageSize]=100`);

        const data = await response.json();
        posts.value = data.data || [];

        console.log('Fetched posts:', posts.value);

        // Automatically parse posts after loading
        parsePostsContent();

    } catch (err) {
        error.value = err.message;
        console.error('Error fetching posts:', err);
    }
};

// Parser function to check content for keywords presence
const parsePostsContent = () => {
    parsedResults.value = posts.value.map(post => {
        const foundKeywords = {};
        const searchableContent = getSearchableContent(post);

        // Check each keyword (case-sensitive)
        searchKeywords.value.forEach(keyword => {
            const regex = new RegExp(`\\b${keyword}\\b`, 'g');
            const matches = searchableContent.match(regex);
            const count = matches ? matches.length : 0;

            if (count > 0) {
                foundKeywords[keyword] = count;
            }
        });

        return {
            id: post.id,
            documentId: post.documentId,
            title: post.title,
            slug: post.slug,
            createdAt: post.createdAt,
            foundKeywords,
            totalMatches: Object.values(foundKeywords).reduce((sum, count) => sum + count, 0),
            matchedKeywordsCount: Object.keys(foundKeywords).length
        };
    });

    console.log('Parsed results:', parsedResults.value);
};

// Get all text content from post for analysis
const getSearchableContent = (post) => {
    let content = '';

    // Add title
    content += post.title || '';
    content += ' ';

    // Add body
    content += post.body || '';
    content += ' ';

    // Add meta information
    content += post.metaTitle || '';
    content += ' ';
    content += post.metaDescription || '';
    content += ' ';
    content += post.ogTitle || '';
    content += ' ';
    content += post.ogDescription || '';
    content += ' ';

    // Add FAQ
    if (post.FAQ && Array.isArray(post.FAQ)) {
        post.FAQ.forEach(faq => {
            content += faq.question || '';
            content += ' ';
            content += faq.answer || '';
            content += ' ';
        });
    }

    return content;
};

// Sorting function
const setSorting = (type, order) => {
    sortBy.value = type;
    sortOrder.value = order;
};

// Sorted results computed property
const sortedResults = computed(() => {
    const results = [...parsedResults.value];

    results.sort((a, b) => {
        let comparison = 0;

        if (sortBy.value === 'date') {
            const dateA = new Date(a.createdAt);
            const dateB = new Date(b.createdAt);
            comparison = dateA - dateB;
        } else if (sortBy.value === 'keywords') {
            comparison = a.matchedKeywordsCount - b.matchedKeywordsCount;
        } else if (sortBy.value === 'matches') {
            comparison = a.totalMatches - b.totalMatches;
        }

        return sortOrder.value === 'asc' ? comparison : -comparison;
    });

    return results;
});

// Statistics across all posts
const totalStatistics = computed(() => {
    const stats = {};

    searchKeywords.value.forEach(keyword => {
        stats[keyword] = {
            totalCount: 0,
            postsWithKeyword: 0
        };
    });

    parsedResults.value.forEach(result => {
        Object.entries(result.foundKeywords).forEach(([keyword, count]) => {
            if (stats[keyword]) {
                stats[keyword].totalCount += count;
                stats[keyword].postsWithKeyword += 1;
            }
        });
    });

    return stats;
});

// Overall summary statistics
const overallSummary = computed(() => {
    let totalMatches = 0;
    let totalPostsWithMatches = 0;

    Object.values(totalStatistics.value).forEach(stat => {
        totalMatches += stat.totalCount;
        if (stat.postsWithKeyword > 0) {
            totalPostsWithMatches += 1; // Count unique keywords that appear in at least one post
        }
    });

    const postsWithAnyMatch = parsedResults.value.filter(result => result.matchedKeywordsCount > 0).length;

    return {
        totalMatches,
        totalPostsWithMatches: postsWithAnyMatch,
        totalKeywords: searchKeywords.value.length
    };
});

// Export to HTML function
const exportToHTML = () => {
    const htmlContent = generateHTMLReport();
    const blob = new Blob([htmlContent], { type: 'text/html;charset=utf-8' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `blog-analysis-report-${new Date().toISOString().split('T')[0]}.html`;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    URL.revokeObjectURL(url);
};

// Generate complete HTML report
const generateHTMLReport = () => {
    const styles = `
        * { box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            padding: 20px;
            max-width: 1200px;
            margin: 0 auto;
            background-color: #ffffff;
            color: #333;
        }
        h1 { font-size: 32px; font-weight: 700;margin-bottom: 30px; color: #2c3e50; }
        h2 { font-size: 24px; margin-top: 30px; margin-bottom: 20px; color: #34495e; }
        h3 { font-size: 18px; margin: 0 0 10px 0; color: #2c3e50; }
        hr { border: none; border-top: 1px solid #e0e0e0; margin: 30px 0; }
        
        .section-header { cursor: pointer; user-select: none; display: flex; align-items: center; gap: 12px; }
        .section-header:hover { color: #3498db; }
        .chevron { display: inline-block; font-size: 16px; color: #3498db; }
        
        .keywords-section { margin-bottom: 30px; }
        .keywords-list { display: flex; flex-wrap: wrap; gap: 10px; }
        .keyword-tag {
            background-color: #3498db;
            color: white;
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 14px;
            font-weight: 500;
        }
        
        .statistics-section { margin-bottom: 40px; }
        .stats-grid {
            margin-top: 12px;
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 15px;
        }
        .summary-stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        .summary-stat-card {
            background-color: #e7f3ff;
            border: 2px solid #b3d9ff;
            border-radius: 12px;
            padding: 20px;
            text-align: center;
        }
        .summary-stat-card h3 {
            margin: 0 0 10px 0;
            font-size: 18px;
            color: #2c3e50;
        }
        .summary-stat-card p {
            margin: 0;
            font-size: 16px;
            color: #495057;
        }
        .summary-stat-card strong {
            color: #2980b9;
            font-size: 24px;
            font-weight: bold;
        }

        .stat-card {
            background-color: #fef2f2;
            border: 1px solid #fecaca;
            border-radius: 8px;
            padding: 15px;
        }
        .stat-card.not-found {
            background-color: #e8f5e8;
            border-color: #c3e6c3;
        }
        .stat-card p { margin: 5px 0; font-size: 14px; color: #6c757d; }
        .stat-card strong { color: #2c3e50; font-size: 16px; }
        
        .results-section { margin-top: 30px; }
        .sorting-info {
            background-color: #f8f9fa;
            border: 1px solid #e9ecef;
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 30px;
            font-size: 14px;
            color: #495057;
        }
        
        .post-result {
            background-color: #fff;
            border: 1px solid #e0e0e0;
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
        }
        .post-header { margin-bottom: 15px; }
        .post-title-row {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 15px;
            margin-bottom: 10px;
        }
        .post-header h3 { font-size: 20px; margin: 0; color: #2c3e50; }
        .post-link { color: #2c3e50; text-decoration: none; transition: color 0.2s ease; }
        .post-link:hover { color: #3498db; text-decoration: underline; }
        .post-date { color: #7f8c8d; font-size: 14px; font-weight: 500; white-space: nowrap; }
        .post-meta { display: flex; flex-wrap: wrap; gap: 10px; }
        .badge {
            background-color: #e9ecef;
            color: #495057;
            padding: 5px 12px;
            border-radius: 12px;
            font-size: 13px;
        }
        
        .found-keywords { margin-top: 15px; }
        .found-keywords strong { color: #2c3e50; margin-bottom: 10px; display: inline-block; }
        .keywords-matches { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 10px; }
        .match-tag {
            background-color: #e48b8b;
            color: white;
            padding: 6px 14px;
            border-radius: 15px;
            font-size: 13px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 5px;
        }
        .match-tag strong { font-weight: bold; margin-bottom: 0; }
        .no-matches { color: #95a5a6; font-style: italic; margin-top: 10px; }
        
        .report-info {
            background-color: #e7f3ff;
            border: 1px solid #b3d9ff;
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 20px;
        }
        .report-info p { margin: 5px 0; }
    `;

    const sortLabels = {
        date: 'Date',
        keywords: 'Keywords Count',
        matches: 'Total Matches'
    };

    let html = `<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blog Content Analysis Report - ${new Date().toLocaleDateString()}</title>
    <style>${styles}</style>
</head>
<body>
    <div class="dev-blog-parser">
        <h1>Blog Content Parser - Analysis Report</h1>
        
        <div class="report-info">
            <p><strong>Generated:</strong> ${new Date().toLocaleString()}</p>
            <p><strong>Total Posts Analyzed:</strong> ${parsedResults.value.length}</p>
            <p><strong>Sorting:</strong> ${sortLabels[sortBy.value]} (${sortOrder.value === 'desc' ? 'Descending' : 'Ascending'})</p>
        </div>
        
        <hr />
        
        <div class="keywords-section">
            <h2>Keywords to Search For</h2>
            <div class="keywords-list">`;
    
    searchKeywords.value.forEach(keyword => {
        html += `<span class="keyword-tag">${keyword}</span>`;
    });
    
    html += `</div></div><hr />`;
    
    // Overall Statistics
    html += `<div class="statistics-section">
            <h2>Overall Statistics</h2>
            <div class="summary-stats">
                <div class="summary-stat-card">
                    <h3>Total Matches Found</h3>
                    <p><strong>${overallSummary.value.totalMatches}</strong> matches across all keywords</p>
                </div>
                <div class="summary-stat-card">
                    <h3>Posts with Matches</h3>
                    <p><strong>${overallSummary.value.totalPostsWithMatches}</strong> / ${parsedResults.value.length} posts have at least one keyword match</p>
                </div>
                <div class="summary-stat-card">
                    <h3>Keywords Analyzed</h3>
                    <p><strong>${overallSummary.value.totalKeywords}</strong> keywords searched</p>
                </div>
            </div>
            <h3>Keyword Breakdown:</h3>
            <div class="stats-grid">`;

    const stats = totalStatistics.value;
    Object.entries(stats).forEach(([keyword, stat]) => {
        const isNotFound = stat.totalCount === 0;
        const cardClass = isNotFound ? 'stat-card not-found' : 'stat-card';
        html += `<div class="${cardClass}">
            <h3>${keyword}</h3>
            <p>Found: <strong>${stat.totalCount}</strong> times</p>
            <p>In posts: <strong>${stat.postsWithKeyword}</strong> / ${parsedResults.value.length}</p>
        </div>`;
    });
    
    html += `</div></div><hr />`;
    
    // Post Results
    html += `<div class="results-section">
            <h2>Post Analysis Results (${parsedResults.value.length})</h2>
            <div class="sorting-info">
                Currently sorted by: <strong>${sortLabels[sortBy.value]}</strong> (${sortOrder.value === 'desc' ? 'Highest to Lowest' : 'Lowest to Highest'})
            </div>`;
    
    sortedResults.value.forEach(result => {
        const postUrl = `https://igaming.mydigicode.com/blog/${result.slug}/`;
        html += `<div class="post-result">
                <div class="post-header">
                    <div class="post-title-row">
                        <h3><a href="${postUrl}" class="post-link" target="_blank">${result.title}</a></h3>
                        <span class="post-date">${new Date(result.createdAt).toLocaleDateString()}</span>
                    </div>
                    <div class="post-meta">
                        <span class="badge">ID: ${result.id}</span>
                        <span class="badge">Keywords found: ${result.matchedKeywordsCount}</span>
                        <span class="badge">Total matches: ${result.totalMatches}</span>
                    </div>
                </div>`;
        
        if (Object.keys(result.foundKeywords).length > 0) {
            html += `<div class="found-keywords">
                    <strong>Found keywords:</strong>
                    <div class="keywords-matches">`;
            
            Object.entries(result.foundKeywords).forEach(([keyword, count]) => {
                html += `<span class="match-tag">${keyword}: <strong>${count}</strong></span>`;
            });
            
            html += `</div></div>`;
        } else {
            html += `<div class="no-matches"><em>No keywords found</em></div>`;
        }
        
        html += `</div>`;
    });
    
    html += `</div></div></body></html>`;
    
    return html;
};

onMounted(() => {
    fetchPosts();
});
</script>

<template>
    <div class="dev-blog-parser">
        <div class="header-row">
            <h1>Blog Content Parser</h1>
            <button 
                v-if="parsedResults.length > 0" 
                @click="exportToHTML" 
                class="export-btn"
            >
                📄 Export as HTML
            </button>
        </div>

        <!-- Error -->
        <div v-if="error" class="error">
            <p>Error: {{ error }}</p>
        </div>

        <hr />

        <!-- Keywords List -->
        <div class="keywords-section">
            <h2 @click="toggleSection('keywords')" class="section-header">
                <span class="chevron" :class="{ expanded: sectionsExpanded.keywords }">▶</span>
                Keywords to Search For ({{ searchKeywords.length }})
            </h2>
            <div v-show="sectionsExpanded.keywords" class="keywords-list">
                <span v-for="keyword in searchKeywords" :key="keyword" class="keyword-tag">
                    {{ keyword }}
                </span>
            </div>
        </div>

        <hr />

        <!-- Overall Statistics -->
        <div v-if="parsedResults.length > 0" class="statistics-section">
            <h2 @click="toggleSection('statistics')" class="section-header">
                <span class="chevron" :class="{ expanded: sectionsExpanded.statistics }">▶</span>
                Overall Statistics
            </h2>
            <div v-show="sectionsExpanded.statistics">
                <div class="summary-stats">
                    <div class="summary-stat-card">
                        <h3>Total Matches Found</h3>
                        <p><strong>{{ overallSummary.totalMatches }}</strong> matches across all keywords</p>
                    </div>
                    <div class="summary-stat-card">
                        <h3>Posts with Matches</h3>
                        <p><strong>{{ overallSummary.totalPostsWithMatches }}</strong> / {{ parsedResults.length }} posts have at least one keyword match</p>
                    </div>
                    <div class="summary-stat-card">
                        <h3>Keywords Analyzed</h3>
                        <p><strong>{{ overallSummary.totalKeywords }}</strong> keywords searched</p>
                    </div>
                </div>
                <h3>Keyword Breakdown:</h3>
                <div class="stats-grid">
                    <div v-for="(stat, keyword) in totalStatistics" :key="keyword" :class="['stat-card', { 'not-found': stat.totalCount === 0 }]">
                        <h3>{{ keyword }}</h3>
                        <p>Found: <strong>{{ stat.totalCount }}</strong> times</p>
                        <p>In posts: <strong>{{ stat.postsWithKeyword }}</strong> / {{ parsedResults.length }}</p>
                    </div>
                </div>
            </div>
        </div>

        <hr />

        <!-- Results by Post -->
        <div v-if="parsedResults.length > 0" class="results-section">
            <h2 @click="toggleSection('results')" class="section-header">
                <span class="chevron" :class="{ expanded: sectionsExpanded.results }">▶</span>
                Post Analysis Results ({{ parsedResults.length }})
            </h2>

            <div v-show="sectionsExpanded.results">
            <!-- Sorting Controls -->
            <div class="sorting-controls">
                <div class="sorting-group">
                    <label>Sort by:</label>
                    <div class="sort-buttons">
                        <button 
                            @click="setSorting('date', 'desc')"
                            :class="{ active: sortBy === 'date' && sortOrder === 'desc' }"
                            class="sort-btn"
                        >
                            Date ↓
                        </button>
                        <button 
                            @click="setSorting('date', 'asc')"
                            :class="{ active: sortBy === 'date' && sortOrder === 'asc' }"
                            class="sort-btn"
                        >
                            Date ↑
                        </button>
                        <button 
                            @click="setSorting('keywords', 'desc')"
                            :class="{ active: sortBy === 'keywords' && sortOrder === 'desc' }"
                            class="sort-btn"
                        >
                            Keywords Count ↓
                        </button>
                        <button 
                            @click="setSorting('keywords', 'asc')"
                            :class="{ active: sortBy === 'keywords' && sortOrder === 'asc' }"
                            class="sort-btn"
                        >
                            Keywords Count ↑
                        </button>
                        <button 
                            @click="setSorting('matches', 'desc')"
                            :class="{ active: sortBy === 'matches' && sortOrder === 'desc' }"
                            class="sort-btn"
                        >
                            Total Matches ↓
                        </button>
                        <button 
                            @click="setSorting('matches', 'asc')"
                            :class="{ active: sortBy === 'matches' && sortOrder === 'asc' }"
                            class="sort-btn"
                        >
                            Total Matches ↑
                        </button>
                    </div>
                </div>
            </div>

            <div v-for="result in sortedResults" :key="result.id" class="post-result">
                <div class="post-header">
                    <div class="post-title-row">
                        <h3>
                            <a :href="`https://igaming.mydigicode.com/blog/${result.slug}/`" class="post-link" target="_blank" rel="noopener noreferrer">
                                {{ result.title }}
                            </a>
                        </h3>
                        <span class="post-date">{{ new Date(result.createdAt).toLocaleDateString() }}</span>
                    </div>
                    <div class="post-meta">
                        <span class="badge">ID: {{ result.id }}</span>
                        <span class="badge">Keywords found: {{ result.matchedKeywordsCount }}</span>
                        <span class="badge">Total matches: {{ result.totalMatches }}</span>
                    </div>
                </div>

                <div v-if="Object.keys(result.foundKeywords).length > 0" class="found-keywords">
                    <strong>Found keywords:</strong>
                    <div class="keywords-matches">
                        <span v-for="(count, keyword) in result.foundKeywords" :key="keyword" class="match-tag">
                            {{ keyword }}: <strong>{{ count }}</strong>
                        </span>
                    </div>
                </div>

                <div v-else class="no-matches">
                    <em>No keywords found</em>
                </div>
            </div>
            </div>
        </div>

        <!-- Loading -->
        <div v-else-if="!error" class="loading">
            <p>Loading and analyzing posts...</p>
        </div>
    </div>
</template>

<style scoped>
.dev-blog-parser {
    padding: 20px;
    max-width: 1200px;
    margin: 0 auto;
    font-family: Stolzl, sans-serif;
}

.header-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 20px;
    margin-bottom: 30px;
    flex-wrap: wrap;
}

.dev-blog-parser h1 {
    font-size: 32px;
    font-weight: 700;
    margin: 0;
    color: #2c3e50;
}

.export-btn {
    background-color: #27ae60;
    color: white;
    border: none;
    padding: 12px 24px;
    border-radius: 8px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    box-shadow: 0 2px 4px rgba(39, 174, 96, 0.3);
    display: flex;
    align-items: center;
    gap: 8px;
}

.export-btn:hover {
    background-color: #229954;
    box-shadow: 0 4px 8px rgba(39, 174, 96, 0.4);
    /* transform: translateY(-2px); */
}

.export-btn:active {
    transform: translateY(0);
    box-shadow: 0 2px 4px rgba(39, 174, 96, 0.3);
}

.dev-blog-parser h2 {
    font-size: 24px;
    margin-top: 30px;
    margin-bottom: 20px;
    color: #34495e;
}

/* Section Headers with Collapse */
.section-header {
    cursor: pointer;
    user-select: none;
    display: flex;
    align-items: center;
    gap: 12px;
    transition: color 0.2s ease;
}

.section-header:hover {
    color: #3498db;
}

.chevron {
    display: inline-block;
    font-size: 16px;
    transition: transform 0.3s ease;
    color: #3498db;
}

.chevron.expanded {
    transform: rotate(90deg);
}

/* Error */
.error {
    background-color: #fee;
    border: 1px solid #fcc;
    border-radius: 8px;
    padding: 15px;
    margin-bottom: 20px;
    color: #c33;
}

/* Keywords */
.keywords-section {
    margin-bottom: 30px;
}

.keywords-list {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
}

.keyword-tag {
    background-color: #3498db;
    color: white;
    padding: 8px 16px;
    border-radius: 20px;
    font-size: 14px;
    font-weight: 500;
}

/* Statistics */
.statistics-section {
    margin-bottom: 30px;
}

.summary-stats {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 20px;
    margin-bottom: 30px;
}

.summary-stat-card {
    background-color: #e7f3ff;
    border: 2px solid #b3d9ff;
    border-radius: 12px;
    padding: 20px;
    text-align: center;
}

.summary-stat-card h3 {
    margin: 0 0 10px 0;
    font-size: 18px;
    color: #2c3e50;
}

.summary-stat-card p {
    margin: 0;
    font-size: 16px;
    color: #495057;
}

.summary-stat-card strong {
    color: #2980b9;
    font-size: 24px;
    font-weight: bold;
}

.stats-grid {
    margin-top: 12px;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 15px;
}

.stat-card {
    background-color: #fef2f2;
    border: 1px solid #fecaca;
    border-radius: 8px;
    padding: 15px;
}

.stat-card.not-found {
    background-color: #e8f5e8;
    border-color: #c3e6c3;
}

.stat-card h3 {
    font-size: 18px;
    margin: 0 0 10px 0;
    color: #2c3e50;
}

.stat-card p {
    margin: 5px 0;
    font-size: 14px;
    color: #6c757d;
}

.stat-card strong {
    color: #2c3e50;
    font-size: 16px;
}

/* Post Results */
.results-section {
    margin-top: 30px;
}

/* Sorting Controls */
.sorting-controls {
    background-color: #f8f9fa;
    border: 1px solid #e9ecef;
    border-radius: 8px;
    padding: 20px;
    margin-bottom: 30px;
}

.sorting-group label {
    font-size: 16px;
    font-weight: 600;
    color: #2c3e50;
    margin-bottom: 12px;
    display: block;
}

.sort-buttons {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
}

.sort-btn {
    background-color: #fff;
    border: 2px solid #dee2e6;
    color: #495057;
    padding: 10px 20px;
    border-radius: 6px;
    font-size: 14px;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s ease;
}

.sort-btn:hover {
    border-color: #3498db;
    color: #3498db;
    background-color: #f0f8ff;
}

.sort-btn.active {
    background-color: #3498db;
    border-color: #3498db;
    color: white;
    box-shadow: 0 2px 6px rgba(52, 152, 219, 0.3);
}

.sort-btn.active:hover {
    background-color: #2980b9;
    border-color: #2980b9;
}

.post-result {
    background-color: #fff;
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    padding: 20px;
    margin-bottom: 20px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.post-header {
    margin-bottom: 15px;
}

.post-title-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 15px;
    margin-bottom: 10px;
}

.post-header h3 {
    font-size: 20px;
    margin: 0;
    color: #2c3e50;
}

.post-link {
    color: #2c3e50;
    text-decoration: none;
    transition: color 0.2s ease;
}

.post-link:hover {
    color: #3498db;
    text-decoration: underline;
}

.post-date {
    color: #7f8c8d;
    font-size: 14px;
    font-weight: 500;
    white-space: nowrap;
}

.post-meta {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
}

.badge {
    background-color: #e9ecef;
    color: #495057;
    padding: 5px 12px;
    border-radius: 12px;
    font-size: 13px;
}

.found-keywords {
    margin-top: 15px;
}

.found-keywords strong {
    color: #2c3e50;
    margin-bottom: 10px;
    display: inline-block;
}

.keywords-matches {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-top: 10px;
}

.match-tag {
    background-color: #e48b8b;
    color: white;
    padding: 6px 14px;
    border-radius: 15px;
    font-size: 13px;
   
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 5px;
}

.match-tag strong {
    font-weight: bold;
    margin-bottom: 0;
}

.no-matches {
    color: #95a5a6;
    font-style: italic;
    margin-top: 10px;
}

/* Loading */
.loading {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}
</style>
