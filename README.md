import { useState, useEffect } from "react";
import { Input } from "@/components/ui/input";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Dialog, DialogTrigger, DialogContent } from "@/components/ui/dialog";

export default function QuranApp() {
  const [search, setSearch] = useState("");
  const [surahs, setSurahs] = useState([]);
  const [filteredSurahs, setFilteredSurahs] = useState([]);
  const [selectedSurah, setSelectedSurah] = useState(null);
  
  useEffect(() => {
    fetch("https://api.alquran.cloud/v1/surah")
      .then((res) => res.json())
      .then((data) => setSurahs(data.data));
  }, []);

  useEffect(() => {
    setFilteredSurahs(
      surahs.filter((surah) =>
        surah.englishName.toLowerCase().includes(search.toLowerCase())
      )
    );
  }, [search, surahs]);

  const fetchSurahDetails = (surahNumber) => {
    fetch(https://api.alquran.cloud/v1/surah/${surahNumber}/editions/quran-uthmani,en.sahih,ru.transliteration,ar.alafasy)
      .then((res) => res.json())
      .then((data) => setSelectedSurah(data.data));
  };

  return (
    <div className="p-4 max-w-2xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">Суры Корана</h1>
      <Input
        placeholder="Поиск суры..."
        value={search}
        onChange={(e) => setSearch(e.target.value)}
        className="mb-4"
      />
      <div className="grid gap-4">
        {filteredSurahs.map((surah) => (
          <Card key={surah.number}>
            <CardContent className="p-4">
              <h2 className="text-lg font-semibold">{surah.englishName} ({surah.name})</h2>
              <p className="text-sm">Аятов: {surah.numberOfAyahs}</p>
              <Dialog>
                <DialogTrigger asChild>
                  <Button className="mt-2" onClick={() => fetchSurahDetails(surah.number)}>Подробнее</Button>
                </DialogTrigger>
                <DialogContent className="p-4 max-h-[500px] overflow-auto">
                  {selectedSurah && selectedSurah[0].number === surah.number && (
                    <div>
                      <h2 className="text-lg font-bold">{selectedSurah[0].englishName}</h2>
                      {selectedSurah[0].ayahs.map((ayah) => (
                        <div key={ayah.number} className="mt-2">
                          <p className="text-right font-semibold">{ayah.text}</p>
                          <p className="text-sm italic">{selectedSurah[1].ayahs.find(a => a.number === ayah.number).text}</p>
                          <p className="text-xs font-semibold">Транскрипция: {selectedSurah[2].ayahs.find(a => a.number === ayah.number).text}</p>
                          <audio controls className="mt-2">
                            <source src={selectedSurah[3].ayahs.find(a => a.number === ayah.number).audio} type="audio/mpeg" />
                            Ваш браузер не поддерживает аудио элемент.
                          </audio>
                        </div>
                      ))}
                    </div>
                  )}
                </DialogContent>
              </Dialog>
            </CardContent>
          </Card>
        ))}
      </div>
    </div>
  );
}
