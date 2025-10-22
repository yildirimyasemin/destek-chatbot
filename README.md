# destek-chatbot

## Proje Linki 
https://huggingface.co/spaces/yildirimyasemin/destek-chatbot

## Chatbot' u test etmek için

Müşterilerin soracağı; iade, kargo, sipariş ve hesap işlemleri gibi konulardan yararlanabilirsiniz. 

### Örnek Sorular 

İade/Değişim --> İade süresi kaç gündür?

Sipariş Takibi --> Siparişimi nasıl takip edebilirim?

Hesap Güvenliği --> Şifremi unuttum, ne yapmalıyım?

Ödeme Yöntemleri --> Kapıda ödeme seçeneğiniz var mı?

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
| **Generation Model** | **Gemini API** | Cevap üretimi (Generation model) ve sorgu çevirisi için kullanılmıştır. |
| **Embedding Model** | **Sentence Transformers** | Türkçe/İngilizce sorgularla İngilizce veri setini eşleştirmek için çok dilli modeli kullanıldı. |
| **Vektör Database** | **FAISS (CPU)** | Yüksek performanslı benzerlik aramasını saniyeler içinde gerçekleştirmek için kullanılmıştır. |
| **Pipeline Framework** | **Custom Python Class** | Hazır bir RAG çerçevesi (LangChain, Haystack ) yerine, tüm RAG adımları özel bir Python sınıfı (`RAGChatbot`) içinde manuel olarak yönetilmiştir. |

###  Prompt Teknikleri

* **Sistem Talimatı (System Instruction):** Modelin rolü (`Kibar ve Profesyonel Asistan`) ve temel kısıtlamaları (`KESİNLİKLE TÜRKÇE`) API çağrısının `system_instruction` parametresi ile kalıcı olarak sabitlenmiştir.
* **Çok Dilli Arama:** Türkçe kullanıcı sorguları, FAISS'te daha doğru sonuçlar bulmak için otomatik olarak İngilizce'ye çevrilmesi sağlanır.
* **Akıllı Geri Dönüş (Graceful Failure):** Kaynakta kesin cevap bulunmayan durumlarda, chatbot tamamen "bilgi yok" demek yerine, **en yakın alakalı bilgiyi** sunar.

## Elde Edilen Sonuçlar Özet 

Proje, hem teknik hem de kullanıcı deneyimi açısından hedeflenen sonuçlara ulaşmıştır:

* **Yüksek Doğruluk:** RAG sayesinde, chatbot'un verdiği cevapların büyük çoğunluğu, sağlanan veri setiyle doğrudan doğrulanır.
* **Çok Dilli Erişim:** Kullanıcılar Türkçe sorular sormasına rağmen, İngilizce veri setinden başarıyla bilgi alınabilmektedir.
* **UX:** Basit selamlama sorularına "bilgi yok" cevapları yerine, `Selamlama Filtresi` sayesinde anında ve uygun bir karşılama yapılmaktadır.

---
