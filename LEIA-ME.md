# R&M Estacionamento — site de produção

## Estrutura

Todos os arquivos ficam **soltos na raiz**, sem subpastas (fotos, vídeo e ícones ao lado do `index.html`). Assim o upload pelo navegador do GitHub funciona sem quebrar caminhos. Para visualizar no computador, abra o `index.html` na mesma pasta dos outros arquivos.

## Arquivos

| Arquivo | Função |
|---|---|
| `index.html` | Landing page completa (CSS e JS embutidos) |
| `404.html` | Página de erro (configurar na hospedagem como página 404) |
| `sitemap.xml` | Enviar no Search Console após publicar |
| `robots.txt` | Libera indexação e aponta o sitemap |
| `favicon.ico`, `apple-touch-icon.png`, `icon-192.png`, `icon-512.png`, `favicon-32.png`, `site.webmanifest` | Ícones gerados a partir da logo |
| `*.webp`, `og-image.jpg`, `logo-512.png` | Fotos em WebP (480/768/1024 px), logo, capa do vídeo e imagem de compartilhamento `og-image.jpg` (1200×630) |
| `tour-rm-estacionamento.mp4` | Tour em vídeo (vertical 576×1024, 52 s, sem áudio, otimizado para começar a tocar antes do download completo) |
| `vercel.json` | Cabeçalhos de segurança e cache para a Vercel |

Publique todos os arquivos na **raiz** do domínio.

## 1. Vídeo (VIDEO_SRC)

Já configurado: `VIDEO_SRC: "tour-rm-estacionamento.mp4"` no bloco `window.SITE_CONFIG` do `<head>`.

- O vídeo é **sem som** (a faixa de áudio foi removida do arquivo), roda **em loop** e **começa sozinho** quando a seção aparece na tela. Ele pausa ao sair da tela para economizar dados e bateria.
- Há um botão discreto de pausar/reproduzir no canto do player. Ele é exigido pelas diretrizes de acessibilidade para conteúdo em movimento com mais de 5 segundos.
- Se o sistema do visitante estiver com "reduzir movimento" ativado, o vídeo não inicia sozinho, e o botão permite tocar.
- Para trocar o vídeo, substitua o arquivo mantendo o nome (MP4 H.264, sem áudio, até ~20 MB). Se `VIDEO_SRC` ficar vazio, aparece só a imagem de capa.

## 2. Google Analytics 4 (GA4_MEASUREMENT_ID)

No mesmo bloco:

```js
GA4_MEASUREMENT_ID: "G-XXXXXXXXXX",
```

Enquanto estiver vazio, nenhum script do Google é carregado. Se preferir o Google Tag Manager, preencha `GTM_CONTAINER_ID` em vez do GA4 e configure o GA4 dentro do GTM. Com GTM preenchido, o gtag direto não é carregado.

### Eventos de conversão implementados

Enviados via `gtag('event', …)` quando há GA4 e sempre via `dataLayer.push({event: …})` para o GTM:

| Evento | Parâmetros | Onde dispara |
|---|---|---|
| `clique_whatsapp` | `secao`: `hero`, `navbar`, `mensal`, `precos`, `cta_final`, `flutuante`; `link_url` | Todos os botões de WhatsApp |
| `clique_telefone` | `link_url` | Telefone na Localização e no rodapé |
| `clique_como_chegar` | `link_url` | Botão "Como chegar" |
| `play_video` | `video_src` | Quando o visitante retoma o vídeo pelo botão |

No GA4, marque `clique_whatsapp` como **evento-chave (conversão)** em Admin → Eventos. O botão "WhatsApp" da navbar usa a mensagem do hero e rótulo próprio (`navbar`), para medir separadamente.

## 3. Google Search Console

No `<head>` do `index.html`, localize o comentário `GOOGLE SEARCH CONSOLE` e descomente a linha:

```html
<meta name="google-site-verification" content="COLE_AQUI_O_CODIGO">
```

Depois de verificar a propriedade, envie `https://rmestacionamento.com.br/sitemap.xml` em Sitemaps e solicite a indexação da página inicial.

## 4. Domínio

O domínio configurado é `https://rmestacionamento.com.br/`. Se o domínio registrado for outro, substitua-o em: `index.html` (canonical, og:url, JSON-LD), `sitemap.xml` e `robots.txt`.

## 5. Pendências e observações

- **Fotos verticais:** as fotos finais estão em 2:3 (retrato). A galeria usa cards verticais (4 colunas no desktop, 2 no tablet, 1 no celular), sem cortar o assunto principal. No hero, a foto da entrada fica como fundo no celular e como um card vertical ao lado do texto no desktop, para não ampliar nem cortar a imagem em telas largas.
- **Coordenadas (geo):** omitidas do JSON-LD para não publicar dados imprecisos. Para incluí-las, copie latitude/longitude exatas do perfil no Google e adicione `"geo": {"@type":"GeoCoordinates","latitude":…,"longitude":…}` ao bloco `ParkingFacility`.
- **Google Business Profile:** mantenha nome, endereço, telefone e horários idênticos aos do site e cadastre o site no perfil.
- **HTTPS e domínio:** na Vercel, configure o domínio principal e redirecione `www` (ou o contrário) para a URL canônica.

## Checklist executado

- Sem scroll horizontal e sem elementos cortados em 320, 360, 375, 390, 414, 768, 1024, 1280, 1440 e 1920 px
- 6 links de WhatsApp com a mensagem correta de cada seção
- Menu mobile (abre, fecha no link e com Esc), lightbox do vídeo (abre e fecha com Esc/botão)
- H1 único; todas as imagens com alt; JSON-LD válido (ParkingFacility + WebSite)
- Nenhum emoji, nenhum tom de verde, nenhuma avaliação ou dado inventado
- Após publicar: validar em search.google.com/test/rich-results e rodar o PageSpeed Insights
