# Sports-betting-bot-
import { useEffect, useState } from 'react'; import { Card, CardContent } from '@/components/ui/card'; import { Button } from '@/components/ui/button';

export default function MLBParlayApp() { const [picks, setPicks] = useState([]); const [loading, setLoading] = useState(true);

useEffect(() => { // Simulated fetch of daily picks const fetchData = async () => { setLoading(true); const data = [ { player: 'Aaron Judge', game: 'Yankees vs Red Sox', bet: 'Home Run', confidence: 'High' }, { player: 'Kyle Schwarber', game: 'Phillies vs Mets', bet: 'Home Run', confidence: 'High' }, { player: 'Shohei Ohtani', game: 'Dodgers vs Giants', bet: 'Home Run', confidence: 'High' }, { player: 'Freddie Freeman', game: 'Dodgers vs Giants', bet: '1+ Hit', confidence: 'Safe' }, { player: 'Yordan Alvarez', game: 'Astros vs Rangers', bet: '1+ RBI', confidence: 'Medium' }, ]; setPicks(data); setLoading(false); };

fetchData();

}, []);

return ( <div className="p-4 max-w-xl mx-auto"> <h1 className="text-2xl font-bold mb-4 text-center">MLB Daily Parlay Picks</h1> {loading ? ( <p className="text-center">Loading picks...</p> ) : ( picks.map((pick, index) => ( <Card key={index} className="mb-3 shadow-md"> <CardContent> <p className="font-semibold text-lg">{pick.player}</p> <p className="text-sm text-gray-600">{pick.game}</p> <p className="text-md">Pick: <strong>{pick.bet}</strong></p> <p className="text-sm text-yellow-600">Confidence: {pick.confidence}</p> </CardContent> </Card> )) )} <div className="text-center mt-6"> <Button onClick={() => window.location.reload()}>🔁 Refresh Picks</Button> </div> </div> ); }
import pandas as pd
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split

# Load your dataset
df = pd.read_csv('matches.csv')

# Feature engineering (example)
df['goal_diff'] = df['home_goals'] - df['away_goals']
df['is_home_win'] = (df['home_goals'] > df['away_goals']).astype(int)

features = ['home_team_rank', 'away_team_rank', 'goal_diff']
X = df[features]
y = df['is_home_win']

# Split data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train model
model = LogisticRegression()
model.fit(X_train, y_train)

# Predict probabilities
probs = model.predict_proba(X_test)[:, 1]  # Probability of home win

# Example: bookmaker's odds for home win
book_odds = 2.5
implied_prob = 1 / book_odds

# Value bet if model's prob > implied prob
value_bets = probs > implied_prob

print("Number of value bets found:", sum(value_bets))
