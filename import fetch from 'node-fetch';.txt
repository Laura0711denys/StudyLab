import fetch from 'node-fetch';

export default async function handler(req, res) {
  const { docs } = req.body;
  const text = docs.map(d => d.text).join("\n\n");

  const prompt = `Maak een samenvatting van de volgende tekst:\n\n${text}`;

  const aiRes = await fetch("https://api.openai.com/v1/chat/completions", {
    method:"POST",
    headers:{
      Authorization:`Bearer ${process.env.OPENAI_API_KEY}`,
      "Content-Type":"application/json"
    },
    body: JSON.stringify({
      model:"gpt-3.5-turbo",
      messages:[{role:"user",content:prompt}],
      max_tokens:500
    })
  });
  const data = await aiRes.json();
  res.status(200).json({ summary:data.choices[0].message.content });
}
