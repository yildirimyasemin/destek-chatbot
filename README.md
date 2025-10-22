# destek-chatbot

# RAG Destek Chatbotu (Gemini API)

Bu proje, Akbank GenAI Bootcamp kapsamında geliştirilmiş bir Retrieval-Augmented Generation (RAG) temelli müşteri destek chatbotudur. Chatbot, kullanıcıların sorularına yalnızca sağlanan SSS verilerine dayanarak, Türkçe olarak yanıt verir.

## Projenin Amacı 

Bu projenin temel amacı, doğal dil anlama yeteneği yüksek, güvenilir bir SSS (Sıkça Sorulan Sorular) asistanı oluşturmaktır.

 **Temel Problem:** GenAI modellerinin "halüsinasyon" (yanlış bilgi üretme) riski.
 **Çözülen Problem:** Kullanıcının Türkçe veya İngilizce dilinde sorduğu sipariş, iade ve hesap yönetimi konularındaki sorulara, hızlı ve bağlama uygun doğru cevaplar üretmek.

## Veri Seti Hakkında Bilgi 

Bu proje, e-ticaret alanına özgü Sıkça Sorulan Sorular (FAQ) ve bu soruların cevaplarını içeren, JSONL formatında bir veri seti kullanmıştır.

| Detay | Açıklama |
| :--- | :--- |
| **İçerik** | Temel olarak hesap yönetimi, iade politikaları, teslimat süreleri ve ödeme yöntemleri gibi müşteri destek konularını kapsar. |
| **Dil** | Veri setinin ana dili İngilizce'dir. |
| **Hazırlık Metodolojisi** | Veri seti, FAISS'te hızlı arama yapılabilmesi için öncelikle **Soru** ve **Cevap** metinleri birleştirilerek tek bir `combined_text` sütunu oluşturulmuş ve bu metinler vektörlere dönüştürülmüştür. |

##  Kullanılan Yöntemler ve Çözüm Mimarisi

Proje , Gemini API'nin gücünü, çok dilli bir arama mekanizmasıyla birleştiren bir RAG mimarisine dayanmaktadır.

### 1. RAG Bileşenleri (Kullanılan Teknolojiler) 

| Kategori | Teknoloji | Açıklama |
| :--- | :--- | :--- |
| **Generation Model** | **Gemini API** | [cite_start]Cevap üretimi (Generation model) [cite: 42] ve sorgu çevirisi için kullanılmıştır. |
| **Embedding Model** | **Sentence Transformers** | [cite_start]Türkçe/İngilizce sorgularla İngilizce veri setini eşleştirmek için kritik çok dilli modeli kullandık[cite: 43]. |
| **Vektör Database** | **FAISS (CPU)** | [cite_start]Yüksek performanslı benzerlik aramasını saniyeler içinde gerçekleştirmek için kullanılmıştır[cite: 43]. |
| **Pipeline Framework** | **Custom Python Class** | [cite_start]Hazır bir RAG çerçevesi (LangChain, Haystack [cite: 44]) yerine, tüm RAG adımları özel bir Python sınıfı (`RAGChatbot`) içinde manuel olarak yönetilmiştir. |

###  Prompt Teknikleri

* **Sistem Talimatı (System Instruction):** Modelin rolü (`Kibar ve Profesyonel Asistan`) ve temel kısıtlamaları (`KESİNLİKLE TÜRKÇE`) API çağrısının `system_instruction` parametresi ile kalıcı olarak sabitlenmiştir.
* **Çok Dilli Arama:** Türkçe kullanıcı sorguları, FAISS'te daha doğru sonuçlar bulmak için otomatik olarak İngilizce'ye çevrilir.
* **Akıllı Geri Dönüş (Graceful Failure):** Kaynakta kesin cevap bulunmayan durumlarda, chatbot tamamen "bilgi yok" demek yerine, **en yakın alakalı bilgiyi** kullanarak mantıklı bir öneri sunar.

## [cite_start] Elde Edilen Sonuçlar Özet [cite: 12]

Proje, hem teknik hem de kullanıcı deneyimi açısından hedeflenen sonuçlara ulaşmıştır:

* **Yüksek Doğruluk:** RAG sayesinde, chatbot'un verdiği cevapların büyük çoğunluğu, sağlanan veri setiyle doğrudan doğrulanır.
* **Çok Dilli Erişim:** Kullanıcılar Türkçe sorular sormasına rağmen, İngilizce veri setinden başarıyla bilgi alınabilmektedir.
* **Daha İyi UX:** Basit selamlama sorularına "bilgi yok" cevapları yerine, `Selamlama Filtresi` sayesinde anında ve uygun bir karşılama yapılmaktadır.

---

## Proje Linki 
