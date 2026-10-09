# Лаборатори №5: API систем тест — Postman ба Newman

## Даалгавар 2: Тест дизайн — сонголт ба утгын хүснэгт

### Тестлэх функц

`POST /registrations` нь оюутны төлөв, хичээлийн оршин байгаа эсэх болон урьдач нөхцөлийг шалгаад оюутныг хичээлд бүртгэнэ. Оролт нь `studentID`, `courseID`; гаралт нь HTTP статус болон JSON хариу байна.

### Сонголт ба төлөөлөх утгын хүснэгт

Эквивалент анги нь ижил үр дүн үүсгэх оролтуудын бүлэг юм. Анги бүрээс төлөөлөх утга сонгож тестийн нөхцөл гаргав.

| Сонголт | Эквивалент анги | Төлөөлөх утга |
|---|---|---|
| studentID-ийн хүчинтэй байдал | Байгаа, идэвхтэй оюутан | `S_ACTIVE`, `status: "active"` |
| studentID-ийн хүчинтэй байдал | Байгаа, идэвхгүй оюутан | `S_INACTIVE`, `status: "inactive"` |
| studentID-ийн хүчинтэй байдал | Оюутан байхгүй | `S_MISSING` — setup-аар үүсгэхгүй |
| Оюутны үзсэн хичээлүүд | Урьдач нөхцөл хангана | `coursesTaken: ["CS201", "CS202"]`, шаардлага нь `["CS201", "CS202"]` |
| Оюутны үзсэн хичээлүүд | Урьдач нөхцөл хангахгүй | `coursesTaken: ["CS201"]`, шаардлага нь `["CS201", "CS202"]` |
| courseID-ийн хүчинтэй байдал | Хичээл байгаа | `CS301` — setup-аар үүсгэнэ |
| courseID-ийн хүчинтэй байдал | Хичээл байхгүй | `CS304` — setup-аар үүсгэхгүй |
| Хичээлийн урьдач нөхцөл | Бүгдийг үзсэн | Шаардлага `["CS201", "CS202"]`, үзсэн `["CS201", "CS202"]` |
| Хичээлийн урьдач нөхцөл | Огт үзээгүй | Шаардлага `["CS201", "CS202"]`, үзсэн `[]` |
| Хичээлийн урьдач нөхцөл | Заримыг үзсэн | Шаардлага `["CS201", "CS202"]`, үзсэн `["CS201"]` |
| Хичээлийн урьдач нөхцөл | Урьдач нөхцөлгүй | `prerequisites: []` |

Урьдач нөхцөлийг хангасан эсэхийг оюутны `coursesTaken` болон хичээлийн `prerequisites` массивыг харьцуулж тодорхойлно.

### Тестийн спецификацийн хүснэгт

Доорх 10 спецификаци нь happy path, дөрвөн алдааны анги, давхар алдааны хоёр хослол болон хязгаарын хоёр тохиолдлыг хамарна. TC06 нь урьдач нөхцөлийн алдааны нэмэлт төлөөлөх тохиолдол юм.

Бүх хүсэлт `POST /registrations` endpoint руу илгээгдэнэ. TC10-аас бусад хүсэлтийн JSON бие нь тухайн мөрийн `studentID`, `courseID`-г агуулна.

| ID | Тестийн нөхцөл | studentID ба оюутны setup | courseID ба хичээлийн setup | Хүлээгдэх HTTP статус | Хүлээгдэх result |
|---|---|---|---|---|---|
| TC01 | Идэвхтэй оюутан бүх урьдач нөхцөлийг хангасан | `S_TC01`: active, coursesTaken = `["CS201", "CS202"]` | `CS301`: prerequisites = `["CS201", "CS202"]` | 201 | `OK` |
| TC02 | Оюутан байхгүй | `S_TC02_MISSING`: үүсгэхгүй | `CS302`: prerequisites = `["CS201"]` | 200 | `ERROR_NO_STUDENT` |
| TC03 | Оюутан идэвхгүй | `S_TC03`: inactive, coursesTaken = `["CS201"]` | `CS303`: prerequisites = `["CS201"]` | 200 | `ERROR_INACTIVE_STUDENT` |
| TC04 | Хичээл байхгүй | `S_TC04`: active, coursesTaken = `["CS201"]` | `CS304`: үүсгэхгүй | 200 | `ERROR_NO_COURSE` |
| TC05 | Урьдач хичээлүүдийг огт үзээгүй | `S_TC05`: active, coursesTaken = `[]` | `CS305`: prerequisites = `["CS201", "CS202"]` | 200 | `ERROR_PREREQUISITES` |
| TC06 | Урьдач хичээлүүдийн заримыг үзсэн | `S_TC06`: active, coursesTaken = `["CS201"]` | `CS306`: prerequisites = `["CS201", "CS202"]` | 200 | `ERROR_PREREQUISITES` |
| TC07 | Оюутан болон хичээл хоёулаа байхгүй | `S_TC07_MISSING`: үүсгэхгүй | `CS307`: үүсгэхгүй | 200 | `ERROR_NO_STUDENT` |
| TC08 | Оюутан идэвхгүй, хичээл байхгүй | `S_TC08`: inactive, coursesTaken = `["CS201"]` | `CS308`: үүсгэхгүй | 200 | `ERROR_INACTIVE_STUDENT` |
| TC09 | Урьдач нөхцөлгүй хичээлд, хичээл үзээгүй оюутан бүртгүүлэх | `S_TC09`: active, coursesTaken = `[]` | `CS309`: prerequisites = `[]` | 201 | `OK` |
| TC10 | Хүсэлтэд courseID талбар дутуу | `S_TC10`: active, coursesTaken = `["CS201"]` | `CS310`: prerequisites = `["CS201"]`; POST хүсэлтээс courseID-г орхино | 400 | `ERROR_BAD_REQUEST` |

Нэмэлт хүлээгдэх утгууд:

- TC05: `missing: ["CS201", "CS202"]`.
- TC06: `missing: ["CS202"]`.
- TC01, TC09: `registrationID` нь тоо байна. Сервер ажиллах хугацаанд утга нь хуримтлагддаг тул яг `1` гэж шалгахгүй.
- TC10: хүсэлтийн бие нь `{"studentID":"S_TC10"}` байна.

Тест бүр өөрийн setup PUT хүсэлтүүдтэй байна. Байхгүй гэж тестлэх ID-г ямар ч setup хүсэлтээр үүсгэхгүй. Тусдаа ID ашигласнаар нэг тестийн setup бусад тестийн өгөгдлийг өөрчлөхөөс сэргийлнэ.

### Давхар алдаа ба боломжгүй хослол

`server.js`-ийн кодын шалгалтын дараалал нь: талбар дутуу → оюутан байхгүй → оюутан идэвхгүй → хичээл байхгүй → урьдач нөхцөл дутуу.

Иймээс TC07-д `ERROR_NO_STUDENT`, TC08-д `ERROR_INACTIVE_STUDENT` хүлээж байна. Эдгээр нь кодоос тодорхойлсон хүлээлт бөгөөд даалгавар 3-ын Postman тестээр баталгаажуулна.

Оюутан байхгүй үед түүний төлөв болон үзсэн хичээлүүдийг тодорхойлох боломжгүй. Хичээл байхгүй үед урьдач нөхцөлийг тодорхойлох боломжгүй тул эдгээр сонголтыг тухайн тохиолдолд “хамаарахгүй” гэж үзнэ. Мөн ижил урьдач нөхцөлийн хувьд “бүгдийг үзсэн” болон “урьдач нөхцөл хангахгүй” ангийг зэрэг сонгох боломжгүй.

## Даалгавар 3: Postman collection

`lab05-collection.json` нь Postman Collection v2.1 форматтай бөгөөд TC01–TC10 тест бүр тусдаа хавтастай. Хавтас бүр setup PUT хүсэлтүүд, `POST /registrations`, HTTP статус болон JSON хариуг шалгах oracle-уудтай. TC07-д үүсгэх өгөгдөл байхгүй тул setup PUT шаардлагагүй.

1. `node server.js` командаар API-г ажиллуулна.
2. Postman → Import → `lab05-collection.json` файлыг сонгоно.
3. Collection-ийн `baseURL` хувьсагчийн анхны утга `http://localhost:3000`.
4. Collection Runner-оор бүх collection-ийг, эсвэл тухайн тестийн хавтсыг бүхэлд нь ажиллуулна. Хавтас доторх setup PUT → POST дарааллыг хадгална.

Тест бүрийн хавтас өөрийн setup PUT хүсэлтүүдтэй тул бусад тестийн setup-аас хамаарахгүй. Нэг тестийг ажиллуулахдаа хавтсыг бүхэлд нь сонгоно; POST-ийг гараар Send хийх бол тухайн хавтасны setup PUT хүсэлтүүдийг эхэлж Send хийнэ. PUT setup бичлэгийг дахин бичдэг тул давтан ажиллуулахад аюулгүй. Байхгүй гэж тестлэх ID-г үүсгэхгүй байх шаардлагатай. `registrationID`-ийн яг утгыг шалгахгүй, тоо эсэхийг шалгана. TC05/TC06 нь `missing` массивын утгыг мөн шалгана.

CLI-ээр серверээ дахин асаалгүй хоёр давталт хийх:

```sh
postman collection run lab05-collection.json --iteration-count 2 --no-report-events
```

Баталгаажуулалт: collection-ийг хоёр давтахад 144 assertion амжилттай, алдаа 0. Тестийн хавтаснуудыг урвуу дарааллаар хоёр давтахад мөн 144 assertion амжилттай, алдаа 0.


## Даалгавар 4: Newman PASS / FAIL / DOWN нотолгоо

Нэг бүтэн ажиллуулалтын тестийн тоо нь **72 assertion** (Newman-ийн `assertions executed`); TC01–TC10 нь 10 тестийн спецификаци бөгөөд setup-тай нийлээд 25 хүсэлт илгээнэ. Өмнөх хоёр давталтын 144 assertion нь 72 × 2 юм.

| Ажиллуулалт | iterations executed / failed | requests executed / failed | assertions executed / failed | Newman exit code | Текст нотолгоо |
|---|---|---|---|---|---|
| PASS | 1 / 0 | 25 / 0 | 72 / 0 | 0 | [newman-pass.txt](results/newman-pass.txt) |
| FAIL | 1 / 0 | 25 / 0 | 72 / 1 | 1 | [newman-fail.txt](results/newman-fail.txt) |
| DOWN | 1 / 0 | 25 / 25 | 50 / 50 | 1 | [newman-down.txt](results/newman-down.txt) |

FAIL-ийн тусдаа [lab05-collection-fail.json](lab05-collection-fail.json) файлд TC01-ийн HTTP oracle-ийг зориуд 201-ээс 200 болгосон: сервер 201 буцаахад `expected response to have status code 200 but got 201` гэж нэг assertion унана. Үндсэн `lab05-collection.json` зөв хэвээр байна. CI quality gate нь Newman-ийн exit code 0 үед амжилттай, 1 үед бүтэлгүй гэж үзнэ.

DOWN-д серверийг SIGINT (Ctrl+C-тэй адил)-ээр зогсоож үндсэн collection-ийг ажиллуулсан; `ECONNREFUSED 127.0.0.1:3000` нь сервертэй холбогдож чадаагүй интерфейсийн алдаа бөгөөд FAIL-ийн зориуд буруу хүлээлттэй oracle-ийн алдаанаас ялгаатай. Хариу ирээгүйгээс JSON унших script-үүд тасалдсан тул DOWN-ийн `assertions executed` 50 байна.

Дахин ажиллуулах (zsh): эхлээд тусдаа терминалд `node server.js` ажиллуулна.

```zsh
mkdir -p results
newman run lab05-collection.json 2>&1 | tee results/newman-pass.txt
run_exit=${pipestatus[1]}
print -r -- "exit=$run_exit" | tee -a results/newman-pass.txt

newman run lab05-collection-fail.json 2>&1 | tee results/newman-fail.txt
run_exit=${pipestatus[1]}
print -r -- "exit=$run_exit" | tee -a results/newman-fail.txt

# Серверийн терминалд Ctrl+C дарсны дараа:
newman run lab05-collection.json 2>&1 | tee results/newman-down.txt
run_exit=${pipestatus[1]}
print -r -- "exit=$run_exit" | tee -a results/newman-down.txt
```

`pipestatus[1]`-ийг pipeline-ийн дараа шууд хадгална; `$?` нь `tee`-ийн exit code тул Newman-ийн үр дүнг илэрхийлэхгүй. Bash-д `run_exit=${PIPESTATUS[0]}` хэрэглэнэ. Нотолгоо нь `results/` доторх текст файлууд юм. DOWN-ийн дараа сервер унтарсан хэвээр байна; дахин ашиглахдаа `node server.js` ажиллуулна.
