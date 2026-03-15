model Question {
  id          String     @id @default(uuid())
  
  // 自己参照（親子関係）
  parentId    String?
  parent      Question?  @relation("QuestionToChildren", fields: [parentId], references: [id])
  children    Question[] @relation("QuestionToChildren")

  // 基本情報
  subjectId     String?
  subject       Subject?   @relation("SubjectToQuestion", fields: [subjectId], references: [id])
  subSubjectId  String?
  subSubject    SubSubject?  @relation("SubSubjectToQuestion", fields: [subSubjectId], references: [id])
  
  type        QuestionType
  
  // 年度（単純な検索なら配列で十分。正規化するならTagと同様に別テーブルへ）
  years       String[]   @relation("YearToQuestion")

  // タグ（多対多のリレーション）
  tags        Tag[]      @relation("TagToQuestion")

  // 本文
  text        String?

  sentence    String?    
}

model Subject {
  id          String     @id @default(uuid())
  name        String     @unique
  subSubjects SubSubject[] @relation("SubjectToSubSubject")
  questions   Question[] @relation("SubjectToQuestion")
}

model SubSubject {
  id          String     @id @default(uuid())
  name        String     @unique
  questions   Question[] @relation("SubSubjectToQuestion")
}

model Tag {
  id          String     @id @default(uuid())
  name        String     @unique
  questions   Question[] @relation("TagToQuestion")
}

model Year {
  id          String     @id @default(uuid())
  name        String     @unique
  questions   Question[] @relation("YearToQuestion")
}


type QuestionType = 
  | 'group' // 大問
  | 'choice' // 選択問題。複数選択も含む
  | 'true_false' // 正誤問題
  | 'short_answer' // 短文、単語、数値解答
  | 'essay'  // 記述
  | 'fill_in_the_blank' // 穴埋め問題