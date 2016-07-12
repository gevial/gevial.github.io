---
title: 'Из книги "Web Operations"'
tags: [book]
---
Прочёл превосходную книгу - "Web Operations: Keeping the Data on Time". Авторы: John Allspaw, Jesse Robbins. ISBN-13: 978-1449377441.

Несмотря на то, что книга вышла в 2010-ом, информация в ней остаётся актуальной для многих компаний.

Также в книге изложены организационные принципы, которые вряд ли устареют в ближайшем будущем.

Хочу особо выделить следующие моменты из книги.

### Составление SLA
SLA (Service Level Agreement) - формальный договор между заказчиком услуги и её поставщиком, содержащий описание услуги, права и обязанности сторон, а также согласованный уровень качества предоставления данной услуги.

> User-facing SLAs have several components (see Table 11-2). You need to be specific about these so that there’s no doubt whether an SLA was violated when someone claims that a problem occurred.

Привожу таблицу 11-2.

| SLA component       | What it means                                                  | How it’s expressed                                                                                                                         | Example |
|---------------------|----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|---------|
| Task being measured | The thing being tested — the business process or function itself | This is usually expressed as a name or description of the test; avoid using just the url or page name as it makes the test harder to read. |“Updating  a contact  record”|
|Metric being  calculated|The element of latency that’s being computed. If you can’t control it, it shouldn’t be in your SLA.|This is a measurement  that is specific and can be reproduced across systems. You should know, for example, that “page load time” means “from the first DNS lookup to the browser’s `onLoad` event.”|“Host latency”|
|Calculation|The math used to generate the number|Unfortunately, this is usually an average. Don’t do this. Averages suck. Insist on a percentile (or  at the very least a trimmed  mean), and a single bad measurement won’t ruin an otherwise good month.|“95th percentile”|
|Valid times|The times and days when the metric is valid. If you don’t include this, you won’t have room for maintenance. Some business processes matter at only certain times.|This is expressed as hours, days, and time zones.|“8:00 a.m. to 9:00  p.m., pST, Monday to Friday”|
|Test conditions (or filters)|The circumstances of the test or the visits included in the report: - For synthetic monitoring, this may be an agreed- upon external service provider. - For RUM, it may be all visits from a particular client, location, IP range, or some other segment.|This is expressed as operating systems, network locations, browsers, user accounts, source IP ranges, and so on.|“Domestic United States on a PC running IE7”|
|Time span|The time over which the calculation is performed|This is expressed as a time range, often a day, week, or month.|“In a 30-day period”|


### Как правильно проводить postmortem-анализ
Postmortem - анализ аварии, который выполняется после восстановления работы в спокойной обстановке. Другое название -  Root Cause Analysis (RCA).
Postmortem должен покрывать следующие базовые вещи:

1.	 A description of the incident
2.	 A description of the root cause
3.	 How the incident was stabilized and/or fixed
4.	 A timeline of actions taken to resolve the incident
5.	 How the incident affected customers
6.	 Remediations or corrective actions
